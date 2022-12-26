#FFmpeg、x264、fdk-aac、openssl的交叉编译

## 简述
以前在Android进行交叉编译都是零零散散的，内容也不是很全，现在重新整理一遍，以便以后参考和移植。本文FFmpeg将集成`x264`、`fdk aac`以及`openssl`第三方库，以及开启`mediacodec`。

Android NDK在版本r17c之后弃用了gcc编译器，改用clang编译器。所以如果ndk在r17c及以下则是使用gcc进行编译。所以以下有两种ndk版本编译：
- NDK17使用gcc编译（FFmpeg最低支持API=21）
- NDK21使用clang编译（FFmpeg最低支持API更低，推荐使用）

## X264
在ffmpeg中使用的h264编码通常是使用x264库内置进去，因为x264的算法更优秀。所以需要把x264编译生成静态库后进行移植。

### 重要参数
- `--prefix`：安装的目录
- `--cross-prefix`：交叉编译的前缀路径，后面会拼接`gcc`、`ld`等
- `--sysroot`：编译过程会去这个目录下找依赖相关的类等
- `--enable-pic`：生成与路径无关静/动态库
- `--extra-cflags`：编译过程需要传递的参数，相关参数参考


### X264在NDK17使用gcc编译
ndk在r17c及以下则是使用gcc进行编译，编译脚本如下：
```c
#!/bin/sh
NDK=/Users/Qincji/Desktop/develop/android/source/sdk/ndk/android-ndk-r17c
API=17
build_x264() {
  ./configure \
    --prefix=${PREFIX} \
    --cross-prefix=${CROSS_PREFIX} \
    --sysroot=${SYSROOT} \
    --host=${HOST} \
    --enable-shared \
    --disable-static \
    --enable-pic \
    --disable-cli \
    --extra-cflags="${EXTRA_CFLAGS}" || exit 0
  make clean
  make install
}

PREFIX=$(pwd)/android_r17c/armeabi-v7a
HOST=arm-linux
CROSS_PREFIX=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64/bin/arm-linux-androideabi-
SYSROOT=$NDK/platforms/android-$API/arch-arm
EXTRA_CFLAGS="-D__ANDROID_API__=$API -isysroot $NDK/sysroot -I$NDK/sysroot/usr/include/arm-linux-androideabi -Os -fPIC -marm"
build_x264
```
注意：`darwin-x86_64`这是平台相关，这里表示MacOS系统，如果是Linux则是`linux-x86_64`，自己的请看NDK路径的。

### X264在NDK21使用clang编译
这是针对NDK在版本r17c之后的。

1）修改x264库跟目录下的`configure`文件
a. 找到`CC="${CC-${cross_prefix}gcc}"`修改成`CC="${CC-$CC_PATH}"`；这里的`CC_PATH`是我们需要添加到环境变量的`clang`编译器路径，居然看编译脚本。
b. 把全局的`${cross_prefix}gcc-`改成`${cross_prefix}-`；也就是把拼接中的`gcc`去掉。

2）看脚本文件：
```c
#!/bin/bash

NDK=/Users/Qincji/Desktop/develop/android/source/sdk/ndk/android-ndk-r21
SYSROOT=$NDK/toolchains/llvm/prebuilt/darwin-x86_64/sysroot
TOOLCHAIN=$NDK/toolchains/llvm/prebuilt/darwin-x86_64
API=21

build_x264() {
  ./configure \
    --prefix=${PREFIX} \
    --cross-prefix=${CROSS_PREFIX} \
    --sysroot=$SYSROOT \
    --host=${HOST} \
    --disable-asm \
    --enable-shared \
    --disable-static \
    --disable-opencl \
    --enable-pic \
    --disable-cli \
    --extra-cflags="$EXTRA_CFLAGS" || exit 0

  make clean
  make install
}

#armeabi-v7a
PREFIX=$(pwd)/android_r21/armeabi-v7a
HOST=arm-linux
#把CC_PATH设置成环境变量，然后在configure中直接读取
export CC_PATH=$TOOLCHAIN/bin/armv7a-linux-androideabi$API-clang
CROSS_PREFIX=$TOOLCHAIN/bin/arm-linux-androideabi-
#EXTRA_CFLAGS="-D__ANDROID_API__=$API -isysroot $NDK/sysroot -I$NDK/sysroot/usr/include/arm-linux-androideabi -Os -fPIC -marm"
build_x264
```

## Fdk-aac
重要参数跟x264的差不过的。

### 编译前的操作
将`libfdkaac/libSBRdec/src/lpp_tran.cpp`中的`__ANDROID__`改成`__ANDROID_OFF__`；也就是去掉`log/log.h`，因为ndk中没有这玩意。

### Fdk-aac在NDK17使用gcc编译
ndk在r17c及以下则是使用gcc进行编译，编译脚本如下：
```c
#!/bin/bash

NDK=/Users/Qincji/Desktop/develop/android/source/sdk/ndk/android-ndk-r17c
TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64
API=17

build_aac() {
  ./configure \
    --prefix=${PREFIX} \
    --host=${HOST} \
    --with-pic \
    --target=android \
    --disable-debug \
    --disable-asm \
    --enable-static=no \
    --enable-shared=yes \ || exit 0

  make clean
  make install
}

#armeabi-v7a
PREFIX=$(pwd)/android_r17c/armeabi-v7a
HOST=arm-linux-androideabi
SYSROOT=$NDK/platforms/android-${API}/arch-arm
CROSS_COMPILE=${TOOLCHAIN}/bin/arm-linux-androideabi-
#以下都是设置环境变量形式就行传递的，查看方式：configure --help
export CC=${CROSS_COMPILE}gcc
export CPP=${CROSS_COMPILE}cpp
export CXX=${CROSS_COMPILE}g++
export CFLAGS="-D__ANDROID_API__=$API --sysroot=${SYSROOT} -isysroot $NDK/sysroot -I$NDK/sysroot/usr/include/arm-linux-androideabi -Os -fPIC -marm"
#-D__ANDROID__=OFF  取消宏定义都没用，也会被覆盖
export CPPFLAGS="${CFLAGS}"
export CXXFLAGS="${CFLAGS}"

export AR=${CROSS_COMPILE}ar
export AS=${CROSS_COMPILE}as
export LD=${CROSS_COMPILE}ld
export RANLIB=${CROSS_COMPILE}ranlib
export STRIP=${CROSS_COMPILE}strip
build_aac
```

### Fdk-aac在NDK21使用clang编译
这是针对NDK在版本r17c之后的。
```c
#!/bin/bash

NDK=/Users/Qincji/Desktop/develop/android/source/sdk/ndk/android-ndk-r21
TOOLCHAIN=$NDK/toolchains/llvm/prebuilt/darwin-x86_64
API=21

build_aac() {
  ./configure \
    --prefix=${PREFIX} \
    --host=${HOST} \
    --with-pic \
    --target=android \
    --disable-debug \
    --disable-asm \
    --enable-static=no \
    --enable-shared=yes \
    CPPFLAGS="-fPIC" || exit 0

  make clean
  make install
}

#armeabi-v7a
PREFIX=$(pwd)/android_r21/armeabi-v7a
HOST=arm-linux-androideabi
export AR=$TOOLCHAIN/bin/arm-linux-androideabi-ar
export AS=$TOOLCHAIN/bin/arm-linux-androideabi-as
export LD=$TOOLCHAIN/bin/arm-linux-androideabi-ld
export RANLIB=$TOOLCHAIN/bin/arm-linux-androideabi-ranlib
export STRIP=$TOOLCHAIN/bin/arm-linux-androideabi-strip
export CC=$TOOLCHAIN/bin/armv7a-linux-androideabi$API-clang
export CXX=$TOOLCHAIN/bin/armv7a-linux-androideabi$API-clang++
build_aac
```

## OpenSSL
### 重要参数
- `--prefix`：安装的目录
- `ANDROID_NDK`：设置环境变量，名称是不能改变的
- `-D__ANDROID_API__=$API`：指定android版本
- `android-arm`：生成架构类型，看官网
- 生成动态库时需要把`.so.xx`中的`.xx`放到前面来，因为android加载动态库时不支持；操作方式：直接全局搜索`.so.`，然后把后面的放到前面来，如：
> '.so.$(SHLIB_VERSION_NUMBER)' --> '.$(SHLIB_VERSION_NUMBER).so'

- 注意：其他的环境变量没有过多的验证

### OpenSSL在NDK17使用gcc编译
ndk在r17c及以下则是使用gcc进行编译，编译脚本如下：
```c
#!/bin/sh

API=17
export ANDROID_NDK=/Users/Qincji/Desktop/develop/android/source/sdk/ndk/android-ndk-r17c
export PATH=$ANDROID_NDK/toolchains/llvm/prebuilt/darwin-x86_64/bin:$ANDROID_NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64/bin:$PATH
export SYSROOT=$ANDROID_NDK/platforms/android-${API}/arch-arm
export TOOLCHAIN=$ANDROID_NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64
CROSS_COMPILE=${TOOLCHAIN}/bin/arm-linux-androideabi-
#以下都是设置环境变量形式就行传递的，查看方式：configure --help
export CC="${CROSS_COMPILE}gcc"
export CFLAGS="-D__ANDROID_API__=$API -isysroot $ANDROID_NDK/sysroot -I$ANDROID_NDK/sysroot/usr/include/arm-linux-androideabi -Os -fPIC -marm"
export STRIP="${CROSS_COMPILE}strip"
export RANLIB="${CROSS_COMPILE}ranlib"
export AR="${CROSS_COMPILE}ar"
export LD="${CROSS_COMPILE}ld"
export AS="${CROSS_COMPILE}as"

./Configure android-arm -D__ANDROID_API__=$API --prefix=$(pwd)/android_r17c/armeabi-v7a

make clean
make -j8
make install
```

### OpenSSL在NDK21使用clang编译
这是针对NDK在版本r17c之后的。
```c
API=21
export ANDROID_NDK=/Users/Qincji/Desktop/develop/android/source/sdk/ndk/android-ndk-r21
export PATH=$ANDROID_NDK/toolchains/llvm/prebuilt/darwin-x86_64/bin:$ANDROID_NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64/bin:$PATH
export SYSROOT=$ANDROID_NDK/platforms/android-${API}/arch-arm
export TOOLCHAIN=$ANDROID_NDK/toolchains/llvm/prebuilt/darwin-x86_64/bin
CROSS_COMPILE=${TOOLCHAIN}/bin/arm-linux-androideabi-
#以下都是设置环境变量形式就行传递的，查看方式：configure --help
export CC="${CROSS_COMPILE}gcc"
export CFLAGS="-D__ANDROID_API__=$API -isysroot $ANDROID_NDK/sysroot -I$ANDROID_NDK/sysroot/usr/include/arm-linux-androideabi -Os -fPIC -marm"
export STRIP="${CROSS_COMPILE}strip"
export RANLIB="${CROSS_COMPILE}ranlib"
export AR="${CROSS_COMPILE}ar"
export LD="${CROSS_COMPILE}ld"
export AS="${CROSS_COMPILE}as"

./Configure android-arm -D__ANDROID_API__=$API --prefix=$(pwd)/android_r21/armeabi-v7a

make clean
make -j8
make install
```

## FFmpeg
为了减少编译的时间以及减少包的体积，这里关闭了很多的参数。

注意：
1）启用`openssl`时需要开启`--pkg-config=pkg-config`，不然会找不着库。
2）使用动态库形式进行引入，不然会报找不到第三库的文件，原因未知。


### 方案一：使用gcc进行编译
```c
#!/bin/bash

#报错了，哈哈
#libavcodec/v4l2_buffers.c:434:44: error: call to 'mmap' declared with attribute error: mmap is not available with _FILE_OFFSET_BITS=64 when using GCC until android-21. Either raise your minSdkVersion, disable _FILE_OFFSET_BITS=64, or switch to Clang.
API=21
export NDK=/Users/Qincji/Desktop/develop/android/source/sdk/ndk/android-ndk-r17c
export SYSROOT=$NDK/platforms/android-$API/arch-arm
export TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64
X264_PREFIX=$(pwd)/libx264/android_r17c/armeabi-v7a
X264_INCLUDE=${X264_PREFIX}/include
X264_LIB=${X264_PREFIX}/lib
FDK_AAC_PREFIX=$(pwd)/libfdkaac/android_r17c/armeabi-v7a
FDK_AAC_INCLUDE=${FDK_AAC_PREFIX}/include
FDK_AAC_LIB=${FDK_AAC_PREFIX}/lib
OPENSSL_PREFIX=$(pwd)/libopenssl/android_r17c/armeabi-v7a
OPENSSL_INCLUDE=${OPENSSL_PREFIX}/include
OPENSSL_LIB=${OPENSSL_PREFIX}/lib

//这里参照下面即可

ARCH=arm
CPU=armv7-a
CROSS_COMPILE=${TOOLCHAIN}/bin/arm-linux-androideabi-
CC=${CROSS_COMPILE}gcc
CXX=${CROSS_COMPILE}g++
CROSS_PREFIX=${CROSS_COMPILE}
PREFIX=$(pwd)/android_r17c/$CPU
OPTIMIZE_CFLAGS="-mfloat-abi=softfp -mfpu=vfp -marm -march=$CPU"
CFLAG="-I${X264_INCLUDE} -I${FDK_AAC_INCLUDE} -I${OPENSSL_INCLUDE} -D__ANDROID_API__=$API --sysroot=${SYSROOT} -isysroot $NDK/sysroot -I$NDK/sysroot/usr/include/arm-linux-androideabi -Os -fPIC $OPTIMIZE_CFLAGS"
build_android
```


### FFmpeg在NDK21使用clang编译
这是针对NDK在版本r17c之后的。
```c
#!/bin/bash

API=21
NDK=/Users/Qincji/Desktop/develop/android/source/sdk/ndk/android-ndk-r21
TOOLCHAIN=$NDK/toolchains/llvm/prebuilt/darwin-x86_64

X264_PREFIX=$(pwd)/libx264/android_r21/armeabi-v7a
X264_INCLUDE=${X264_PREFIX}/include
X264_LIB=${X264_PREFIX}/lib

FDK_AAC_PREFIX=$(pwd)/libfdkaac/android_r21/armeabi-v7a
FDK_AAC_INCLUDE=${FDK_AAC_PREFIX}/include
FDK_AAC_LIB=${FDK_AAC_PREFIX}/lib

OPENSSL_PREFIX=$(pwd)/libopenssl/android_r21/armeabi-v7a
OPENSSL_INCLUDE=${OPENSSL_PREFIX}/include
OPENSSL_LIB=${OPENSSL_PREFIX}/lib

function build_android() {
  ./configure \
    --prefix=$PREFIX \
    --disable-opencl \
    --disable-doc \
    --disable-everything \
    --disable-htmlpages \
    --disable-podpages \
    --disable-debug \
    --disable-programs \
    --disable-demuxers \
    --disable-muxers \
    --disable-decoders \
    --disable-encoders \
    --disable-bsfs \
    --disable-indevs \
    --disable-outdevs \
    --disable-filters \
    --disable-protocols \
    --disable-hwaccels \
    --disable-avdevice \
    --disable-postproc \
    --disable-devices \
    --disable-symver \
    --disable-stripping \
    --disable-asm \
    --disable-w32threads \
    --disable-parsers \
    --disable-static \
    --enable-shared \
    --enable-openssl \
    --pkg-config=pkg-config \
    --enable-gpl \
    --enable-nonfree \
    --enable-libx264 \
    --enable-encoder=libx264 \
    --enable-swscale \
    --enable-protocol=file \
    --enable-protocol=hls \
    --enable-protocol=rtmp \
    --enable-protocol=http \
    --enable-protocol=tls \
    --enable-protocol=https \
    --enable-demuxer=aac \
    --enable-demuxer=h264 \
    --enable-demuxer=mov \
    --enable-demuxer=flv \
    --enable-demuxer=avi \
    --enable-demuxer=hevc \
    --enable-demuxer=gif \
    --enable-parser=h264 \
    --enable-parser=hevc \
    --enable-parser=aac \
    --enable-decoder=aac \
    --enable-decoder=h264 \
    --enable-decoder=flv \
    --enable-decoder=gif \
    --enable-decoder=mpeg4 \
    --enable-bsf=aac_adtstoasc \
    --enable-bsf=h264_mp4toannexb \
    --enable-jni \
    --enable-mediacodec \
    --enable-decoder=h264_mediacodec \
    --enable-hwaccel=h264_mediacodec \
    --enable-decoder=hevc_mediacodec \
    --enable-decoder=mpeg4_mediacodec \
    --enable-decoder=vp8_mediacodec \
    --enable-decoder=vp9_mediacodec \
    --enable-neon \
    --enable-gpl \
    --cross-prefix=$CROSS_PREFIX \
    --target-os=android \
    --arch=$ARCH \
    --cpu=$CPU \
    --cc=$CC \
    --cxx=$CXX \
    --enable-cross-compile \
    --sysroot=$SYSROOT \
    --extra-cflags="-I${X264_INCLUDE} -I${FDK_AAC_INCLUDE} -I${OPENSSL_INCLUDE} -Os -fpic $OPTIMIZE_CFLAGS" \
    --extra-ldflags="-L${X264_LIB} -L${FDK_AAC_LIB} -L${OPENSSL_LIB} $ADDI_LDFLAGS" || exit 1
  make clean
  make -j8
  make install
}

#armv8-a
ARCH=arm64
CPU=armv8-a
CC=$TOOLCHAIN/bin/aarch64-linux-android$API-clang
CXX=$TOOLCHAIN/bin/aarch64-linux-android$API-clang++
SYSROOT=$NDK/toolchains/llvm/prebuilt/darwin-x86_64/sysroot
CROSS_PREFIX=$TOOLCHAIN/bin/aarch64-linux-android-
PREFIX=$(pwd)/android/$CPU
OPTIMIZE_CFLAGS="-march=$CPU"
#build_android

#armv7-a
ARCH=arm
CPU=armv7-a
CC=$TOOLCHAIN/bin/armv7a-linux-androideabi$API-clang
CXX=$TOOLCHAIN/bin/armv7a-linux-androideabi$API-clang++
SYSROOT=$NDK/toolchains/llvm/prebuilt/darwin-x86_64/sysroot
CROSS_PREFIX=$TOOLCHAIN/bin/arm-linux-androideabi-
PREFIX=$(pwd)/android-so/$CPU
OPTIMIZE_CFLAGS="-mfloat-abi=softfp -mfpu=vfp -marm -march=$CPU "
build_android

#x86
ARCH=x86
CPU=x86
CC=$TOOLCHAIN/bin/i686-linux-android$API-clang
CXX=$TOOLCHAIN/bin/i686-linux-android$API-clang++
SYSROOT=$NDK/toolchains/llvm/prebuilt/darwin-x86_64/sysroot
CROSS_PREFIX=$TOOLCHAIN/bin/i686-linux-android-
PREFIX=$(pwd)/android/$CPU
OPTIMIZE_CFLAGS="-march=i686 -mtune=intel -mssse3 -mfpmath=sse -m32"
#build_android

#x86_64
ARCH=x86_64
CPU=x86-64
CC=$TOOLCHAIN/bin/x86_64-linux-android$API-clang
CXX=$TOOLCHAIN/bin/x86_64-linux-android$API-clang++
SYSROOT=$NDK/toolchains/llvm/prebuilt/darwin-x86_64/sysroot
CROSS_PREFIX=$TOOLCHAIN/bin/x86_64-linux-android-
PREFIX=$(pwd)/android/$CPU
OPTIMIZE_CFLAGS="-march=$CPU -msse4.2 -mpopcnt -m64 -mtune=intel"
#build_android
```

## 导入到Android Studio中进行验证
1）在app级的`build.gradle`中设置动态库目录：
```java
android {
    defaultConfig {
        externalNativeBuild {
            cmake {
                cppFlags ""
                abiFilters 'armeabi-v7a'
            }
        }
    }

    externalNativeBuild {
        cmake {
            path "src/main/cpp/CMakeLists.txt"
            //            version "3.10.2" //版本过高导致无日志输出
            version "3.6.0"
        }
    }

    //配置第三方so路径
    sourceSets {
        main {
            jniLibs.srcDirs = ['src/main/cpp/libs']
        }
    }
}

dependencies {
}
```

2）以下为CMakeList.txt导入方式：
```cmake
#1、添加版本
cmake_minimum_required(VERSION 3.4.1)

include_directories(include)
file(GLOB core_srcs ${CMAKE_SOURCE_DIR}/core/*.cpp)

#2、编译库，把native-lib.cpp编译成以native-lib为名称的动态库
add_library(
        native-lib
        SHARED
        player_main.cpp ${core_srcs})

#3、设置C++编译参数（CMAKE_CXX_FLAGS是全局变量）
#添加其他预编译库还可以使用这种方式
#使用-L指导编译时库文件的查找路径
#cxx_flags "cxx_flags -L目录"
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -L${CMAKE_SOURCE_DIR}/libs/${CMAKE_ANDROID_ARCH_ABI}")

#4、链接到库文件，jni/c/c++可以引入链接到
target_link_libraries(
        native-lib
        log
        # 先把有依赖的库，先依赖进来；注意顺序。
        avformat avcodec avfilter avutil swresample swscale
        x264.161
        fdk-aac
        ssl.3 crypto.3 #openssl
        z
)
```
3）在java类中进行加载
```java
static {
    //动态库的操作 avformat avcodec avfilter avutil swresample swscale
    System.loadLibrary("avformat");
    System.loadLibrary("avcodec");
    System.loadLibrary("avfilter");
    System.loadLibrary("avutil");
    System.loadLibrary("swresample");
    System.loadLibrary("swscale");
    System.loadLibrary("x264.161");
    System.loadLibrary("fdk-aac");
    System.loadLibrary("ssl.3");
    System.loadLibrary("crypto.3");
    System.loadLibrary("native-lib");
}
```

具体的请看[Demo地址：FFmpegImportDemo](https://github.com/xhunmon/AFPlayer/tree/main/FFmpegImportDemo)

 

## 参考
- https://github.com/taoliuh/ffmpeg-build-tools
- https://github.com/ShikinChen/android_ffmpeg
- https://github.com/hilive/build-script
- https://github.com/lllkey/android-openssl-build
- https://www.jianshu.com/p/f98db1d84d93
- https://www.jianshu.com/p/f98db1d84d93

---------
- [0基础学习音视频路线，以及重磅音视频资料下载](https://github.com/xhunmon/VABlog) 求赞。
---------
