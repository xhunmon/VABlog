#JNI


##简述
JNI全称：Java Native Interface（Java本地接口）。JNI是提供一种Java字节码调用C/C++的解决方案，描述的是一种技术。简单来说，Java是通过JNI调用C/C++的。


##JNI的使用

JNI的使用大概类似如下图所示：

![](./04/jni_native.jpg)


###1）Java加载和链接本地方法

```java
package com.test;
class Cls {
    native String fun(int i);
    static {
        System.loadLibrary("pkg_Cls");
    }
}
```

其中，`native`关键字是表示调用的使用`C/C++`的方法；参数`pkg_Cls`是链接动态库的名字，可以随便取。在`Linux`系统中会自动加上前缀`lib`和后缀`.so`，即加载的是`libpkg_Cls.so`文件。而在`Win32`系统中，将转化为`pkg_Cls.dll`。


###2）JNI方法注册
Java调用的`native`本地方法需要先在JNI层进行函数注册才能够使用。而函数注册的方式有静态注册和动态注册两种。

####静态注册

当Java层调用`native`函数时，会在JNI库中根据函数名查找对应的JNI函数。如果没找到会报错。如果找到了，则会在`native`函数与JNI函数之间建立关联关系，其实就是保存JNI函数的函数指针。下次再调用`native`函数，就可以直接使用这个函数指针。

**1.JNI函数名称**

格式（需要将"."转换成"_"）：
> extern "C" JNIEXPORT 返回值类型 JNICALL
> Java_+包名+类名+函数名(JNIEnv* env, Java层参数)

上面例子：
> extern "C" JNIEXPORT jstring JNICALL
> Java_com_test_Cls_fun(JNIEnv* env, jint i)

**2.参数说明**

`extern "C"`：用来在C++程序中声明或定义一个C的符号。因为C++能进行函数重载而C则不能，如果把参数内容当做C++来编译回去出现函数参数不匹配的错误。所以，C++编译器会将在`extern “C”`的大括号内部的代码当做C语言来处理。


####动态注册


##JNI的原理




