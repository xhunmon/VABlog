# JNI


## 简述
JNI全称：Java Native Interface（Java本地接口）。JNI是提供一种Java字节码调用C/C++的解决方案，描述的是一种技术。简单来说，Java是通过JNI调用C/C++的。

## 数据类型映射

|   Java类型    |  Native类型   |     描述符     | 
|:-----------:|:-----------:|:-----------:| 
|   boolean   |  jboolean   |    Z（特殊）    | 
|    byte     |    jbyte    |      B      | 
|    char     |    jchar    |      C      | 
|    short    |   jshort    |      S      | 
|     int     |    jint     |      I      | 
|    long     |    jlong    |      J（特殊）      | 
|    float    |   jfloat    |      F      | 
|   double    |   jdouble   |      D      | 
|    void     |    void     |      V      | 
|   Object    |   jobject   | LClassName; |

> 其中ClassName为具体的类名，以分号结尾。注意，Java中的基本数据类型除了void都有对应的j开头的Native类型，而Obj
ect类型被映射为jobject类型。如，java中的字符串：Ljava/lang/String;

在涉及到数组时，Java中的数组与指向数组元素的指针在JNI中等价。数组类型转换也非常简单：取数组元素类型的描述符
面加上[即可。例如Java中的int[]对应的Native类型为[I。

下表为Java数组类型与对应的Native类型及描述符的映射表：

|     Java数组类型      | Native类型   | 描述符    | 
|:-----------------:| :-----------: | :-------: | 
|     boolean[]     | jbooleanArray      | [Z         | 
|      byte[]       | jbyteArray         | [B         | 
|      char[]       |    jcharArray     | [C         | 
|      short[]      | jshortArray        | [S         | 
|       int[]       |     jintArray     | [I         | 
|      long[]       | jlongArray         | [J         | 
|      float[]      |    jfloatArray    | [F         | 
|     double[]      | jdoubleArray       | [D         | 
|  Object[]         |   jobjectArray    | [LClassName;|

> 其中ClassName为具体的类名，以分号结尾。注意，数组类型的描述符要在前面加上[来表示。例如，int[][]对应的描述符
为[[I。

## JNI的使用

JNI的使用大概类似如下图所示：

![](img/04/jni_native.jpg)

### 静态注册案例

1、首先，定义一个Java类，在其中声明native方法：
```java
public class MyUtils {                                                                                    
     static {                                                                                              
         System.loadLibrary("native-lib");                                                                 
     }                                                                                                     
     public static native int add(int a, int b);                                                           
     public static native String concatenate(String str1, String str2);                                    
     public static native void arrayTest(int[] arr);                                                       
     public static native int[] sumArrays(int[] arr1, int[] arr2);                                         
 }  
```

其中，我们定义了四个native方法，分别是：

• add()方法，接收两个整型参数并返回它们的和。                                                             
• concatenate()方法，接收两个字符串并将它们拼接在一起返回。                                               
• arrayTest()方法，接收一个整型数组。                                                                     
• sumArrays()方法，接收两个整型数组，将它们对应的元素相加，返回一个新的整型数组。

2、然后，我们需要在C/C++中实现这些方法。下面是一个示例实现：
```c++
#include "MyUtils.h"                                                                                      
 #include <cstring>                                                                                        
                                                                                                           
 extern "C"                                                                                                
 JNIEXPORT jint JNICALL                                                                                    
 Java_com_example_MyUtils_add(JNIEnv *env, jclass clazz, jint a, jint b) {                                 
     return a + b;                                                                                         
 }                                                                                                         
                                                                                                           
 extern "C"                                                                                                
 JNIEXPORT jstring JNICALL                                                                                 
 Java_com_example_MyUtils_concatenate(JNIEnv *env, jclass clazz, jstring str1, jstring str2) {             
     const char *cStr1 = env->GetStringUTFChars(str1, nullptr);                                            
     const char *cStr2 = env->GetStringUTFChars(str2, nullptr);                                            
                                                                                                           
     char buffer[256];                                                                                     
     strcpy(buffer, cStr1);                                                                                
     strcat(buffer, cStr2);                                                                                
     env->ReleaseStringUTFChars(str1, cStr1);                                                              
     env->ReleaseStringUTFChars(str2, cStr2);                                                              
                                                                                                           
     return env->NewStringUTF(buffer);                                                                     
 }                                                                                                         
                                                                                                           
 extern "C"                                                                                                
 JNIEXPORT void JNICALL                                                                                    
 Java_com_example_MyUtils_arrayTest(JNIEnv *env, jclass clazz, jintArray arr) {                            
     jint *cArr = env->GetIntArrayElements(arr, nullptr);                                                  
     jsize len = env->GetArrayLength(arr);                                                                 
                                                                                                           
     for (int i = 0; i < len; ++i) {                                                                       
         cArr[i]++;                                                                                        
     }                                                                                                     
                                                                                                           
     env->ReleaseIntArrayElements(arr, cArr, JNI_COMMIT);                                                  
 }                                                                                                         
                                                                                                           
 extern "C"                                                                                                
 JNIEXPORT jintArray JNICALL                                                                               
 Java_com_example_MyUtils_sumArrays(JNIEnv *env, jclass clazz, jintArray arr1, jintArray arr2) {           
     jint *cArr1 = env->GetIntArrayElements(arr1, nullptr);                                                
     jint *cArr2 = env->GetIntArrayElements(arr2, nullptr);                                                
     jsize len = env->GetArrayLength(arr1);                                                                
                                                                                                           
     jintArray result = env->NewIntArray(len);                                                             
     jint *cResult = env->GetIntArrayElements(result, nullptr);                                            
                                                                                                           
     for (int i = 0; i < len; ++i) {                                                                       
         cResult[i] = cArr1[i] + cArr2[i];                                                                 
     }                                                                                                     
                                                                                                           
     env->ReleaseIntArrayElements(arr1, cArr1, JNI_ABORT);                                                 
     env->ReleaseIntArrayElements(arr2, cArr2, JNI_ABORT);                                                 
     env->ReleaseIntArrayElements(result, cResult, JNI_COMMIT);                                            
                                                                                                           
     return result;                                                                                        
 } 
```
> 在C/C++代码中，每个native方法都需要使用externC进行声明，并以stdcall方式实现。在实现每个方法时，需要根据参数类型在JNI API中进行数据类型转换操作

3、完成上述代码之后，我们需要编译生成动态链接库文件。以Android Studio为例，可以在build.gradle中添加以下配置：

```shell
android {                                                                                                 
     // ...                                                                                                
     defaultConfig {                                                                                       
         // ...                                                                                            
         externalNativeBuild {                                                                             
             cmake {                                                                                       
                 cppFlags ""                                                                               
                 // 指定C/C++文件所在的目录                                                                
                 arguments "-DCMAKE_CXX_FLAGS=-I${projectDir}/src/main/cpp/include"                        
             }                                                                                             
         }                                                                                                 
     }                                                                                                     
     // ...                                                                                                
     externalNativeBuild {                                                                                 
         cmake {                                                                                           
             // 指定CMakeLists.txt所在的目录                                                               
             path "src/main/cpp/CMakeLists.txt"                                                            
         }                                                                                                 
     }                                                                                                     
 }       
```
然后，我们在CMakeLists.txt中添加以下内容：
```cmake
 cmake_minimum_required(VERSION 3.4.1)                                                                     
                                                                                                           
 # 添加头文件目录                                                                                          
 include_directories(${CMAKE_SOURCE_DIR}/include)                                                          
                                                                                                           
 # 添加源文件                                                                                              
 add_library(native-lib SHARED                                                                             
             MyUtils.cpp )                                                                                 
                                                                                                           
 # 搜索并链接静态库                                                                                        
 find_library(log-lib log)                                                                                 
                                                                                                           
 target_link_libraries(native-lib                                                                          
                       ${log-lib} )                                                                        
                                     
```
> 其中，include_directories指令用于添加头文件目录，add_library指令用于添加源文件，find_library指令用于搜索并
接静态库，target_link_libraries指令用于链接库。

4、在应用程序运行时，加载动态链接库文件时，静态注册的native方法就可以使用了。例如可以通过以下方式调用：
```java
 // 调用add方法                                                                                            
 int result = MyUtils.add(1, 2);                                                                           
                                                                                                           
 // 调用concatenate方法                                                                                    
 String s = MyUtils.concatenate("hello", "world");                                                         
                                                                                                           
 // 调用arrayTest方法                                                                                      
 int[] arr = {1, 2, 3};                                                                                    
 MyUtils.arrayTest(arr);                                                                                   
                                                                                                           
 // 调用sumArrays方法                                                                                      
 int[] arr1 = {1, 2, 3};                                                                                   
 int[] arr2 = {4, 5, 6};                                                                                   
 int[] result = MyUtils.sumArrays(arr1, arr2);    
```


### 动态注册案例

将上面静态注册案例的第2步改成以下即可：
```c++
#include "jni.h"                                                                                          
 #include <cstring>                                                                                        
                                                                                                           
 jint my_add(JNIEnv *env, jobject obj, jint a, jint b) {                                                   
     return a + b;                                                                                         
 }                                                                                                         
                                                                                                           
 jstring my_concatenate(JNIEnv *env, jobject obj, jstring str1, jstring str2) {                            
     const char *cStr1 = env->GetStringUTFChars(str1, nullptr);                                            
     const char *cStr2 = env->GetStringUTFChars(str2, nullptr);                                            
                                                                                                           
     char buffer[256];                                                                                     
     strcpy(buffer, cStr1);                                                                                
     strcat(buffer, cStr2);                                                                                
     env->ReleaseStringUTFChars(str1, cStr1);                                                              
     env->ReleaseStringUTFChars(str2, cStr2);                                                              
                                                                                                           
     return env->NewStringUTF(buffer);                                                                     
 }                                                                                                         
                                                                                                           
 void my_arrayTest(JNIEnv *env, jobject obj, jintArray arr) {                                              
     jint *cArr = env->GetIntArrayElements(arr, nullptr);                                                  
     jsize len = env->GetArrayLength(arr);                                                                 
                                                                                                           
     for (int i = 0; i < len; ++i) {                                                                       
         cArr[i]++;                                                                                        
     }                                                                                                     
                                                                                                           
     env->ReleaseIntArrayElements(arr, cArr, JNI_COMMIT);                                                  
 }                                                                                                         
                                                                                                           
 jintArray my_sumArrays(JNIEnv *env, jobject obj, jintArray arr1, jintArray arr2) {                        
     jint *cArr1 = env->GetIntArrayElements(arr1, nullptr);                                                
     jint *cArr2 = env->GetIntArrayElements(arr2, nullptr);                                                
     jsize len = env->GetArrayLength(arr1);                                                                
                                                                                                           
     jintArray result = env->NewIntArray(len);                                                             
     jint *cResult = env->GetIntArrayElements(result, nullptr);                                            
                                                                                                           
     for (int i = 0; i < len; ++i) {                                                                       
         cResult[i] = cArr1[i] + cArr2[i];                                                                 
     }                                                                                                     
                                                                                                           
     env->ReleaseIntArrayElements(arr1, cArr1, JNI_ABORT);                                                 
     env->ReleaseIntArrayElements(arr2, cArr2, JNI_ABORT);                                                 
     env->ReleaseIntArrayElements(result, cResult, JNI_COMMIT);                                            
                                                                                                           
     return result;                                                                                        
 }                                                                                                         
                                                                                                           
 // 定义native方法数组                                                                                     
 static JNINativeMethod nativeMethods[] = {                                                                
         {"add", "(II)I", (void *) my_add},                                                                
         {"concatenate", "(Ljava/lang/String;Ljava/lang/String;)Ljava/lang/String;", (void *)              
 my_concatenate},                                                                                          
         {"arrayTest", "([I)V", (void *) my_arrayTest},                                                    
         {"sumArrays", "([I[I)[I", (void *) my_sumArrays},                                                 
 };                                                                                                        
                                                                                                           
 // 动态注册native方法                                                                                     
 JNIEXPORT jint JNICALL                                                                                    
 JNI_OnLoad(JavaVM *vm, void *reserved) {                                                                  
     JNIEnv *env;                                                                                          
     if (vm->GetEnv((void **)&env, JNI_VERSION_1_6) != JNI_OK) {                                           
         return -1;                                                                                        
     }                                                                                                     
                                                                                                           
     jclass clazz = env->FindClass("com/example/MyUtils");                                                 
     env->RegisterNatives(clazz, nativeMethods, sizeof(nativeMethods) / sizeof(nativeMethods[0]));         
                                                                                                           
     return JNI_VERSION_1_6;                                                                               
 }  
```

### 相比于静态注册，动态注册有以下优点：
- 1 灵活性更高：动态注册的方式可以让开发者在运行时根据需要动态绑定native方法，灵活性更高。                  
- 2 代码更加简洁：相比于静态注册，动态注册不需要在Java类中声明native方法，因此Java代码更加简洁。            
- 3 更好的代码可读性和可维护性：所有native方法的实现都集中在对应的C/C++代码中，代码的可读性和可维护性更高。
- 4 减少编译时间：相比于静态注册，在动态注册的情况下，每次修改native方法的实现不需要重新编译Java类，仅需要重native的实现即可，可以大大减少编译时间。


### Java对象传递给Native

1、Java代码如下所示：
```java
 public class Person {                                                                                     
     public String name;                                                                                   
     public int age;                                                                                       
     public boolean gender;                                                                                
 }                                                                                                         
```
2、C++代码如下所示：
```c++
struct PersonInfo {                                                                                       
     char *name;                                                                                           
     int age;                                                                                              
     bool gender;                                                                                          
 };                                                                                                        
                                                                                                           
 extern "C"                                                                                                
 JNIEXPORT void JNICALL                                                                                    
 Java_com_example_MyUtils_processPerson(JNIEnv *env, jclass clazz, jobject obj) {                          
     // 将Java对象转换为PersonInfo结构体                                                                   
     jfieldID nameFieldID = env->GetFieldID(clazz, "name", "Ljava/lang/String;");                          
     jfieldID ageFieldID = env->GetFieldID(clazz, "age", "I");                                             
     jfieldID genderFieldID = env->GetFieldID(clazz, "gender", "Z");                                       
                                                                                                           
     jstring nameObj = (jstring) env->GetObjectField(obj, nameFieldID);                                    
     jint age = env->GetIntField(obj, ageFieldID);                                                         
     jboolean gender = env->GetBooleanField(obj, genderFieldID);                                           
                                                                                                           
     const char *nameChars = env->GetStringUTFChars(nameObj, nullptr);                                     
                                                                                                           
     PersonInfo personInfo;                                                                                
     personInfo.name = const_cast<char *>(nameChars);                                                      
     personInfo.age = age;                                                                                 
     personInfo.gender = gender;                                                                           
                                                                                                           
     // ...                                                                                                
 }   
```
> 在C++代码中，我们需要使用JNI API来获取Java对象的Field ID，之后通过GetObjectField()、GetIntField()和GetBooleanField()等方法来获取Java对象的内容。最后，我们将获取到的信息填充到C++中的结构体PersonInfo中，即可在native代码中使用该对象。
注意，在实现过程中需要注意使用ReleaseXX()方法释放对Java对象的引用计数。

### Native对象传递给Java（Native回调Java）

1 在 Java 层中定义 Native 方法。例如：
```java
public native void nativeMethod();
```

2 在 Native 层中实现该方法，并通过 JNI 找到对应的 Java 方法。


```c++
JNIEXPORT void JNICALL                                                                                    
Java_com_example_MyClass_nativeMethod(JNIEnv *env, jobject) {                                             
// 处理数据                              `                                                             
...                                                                                                   
// 通过 JNI 找到 Java 方法，并将数据传递回去                                                          
jclass cls = env->GetObjectClass(obj); // obj 为 Java 层的对象，可以通过参数传递过来                  
jmethodID methodID = env->GetMethodID(cls, "setData", "(Ljava/lang/String;)V");                       
jstring data = env->NewStringUTF("Hello Java");                                                       
env->CallVoidMethod(obj, setMethodID, data);                                                          
}
```

### 多线程中，native如何回调java？

1 在 Java 层中定义 Native 方法和回调方法。Native 方法使用 synchronized                                    
关键字进行同步，保证多线程安全。例如：
```java
public class MyClass {                                                                                    
    public synchronized native void nativeMethod();

     // 该方法将被 Native 层回调                                                                           
     public void callback(String data) {                                                                   
         // 处理 Native 层返回的数据                                                                       
     }                                                                                                     
}
```


2 在 Native 层中实现该方法，并通过 JNI 找到对应的 Java方法，并使用全局引用。全局引用的对象能够在多个线程间共享。
```c++
// 定义全局引用                                                                                           
 static jclass g_cls = NULL;                                                                               
 static jobject g_obj = NULL;                                                                              
 static jmethodID g_callbackMethodID = NULL;

 static JavaVM *g_jvm = NULL;

 JNIEXPORT jint JNICALL
 JNI_OnLoad(JavaVM *vm, void *reserved) {
     JNIEnv *env;
     if (vm->GetEnv((void **)&env, JNI_VERSION_1_6) != JNI_OK) {
        return JNI_ERR;
     }
     g_jvm = vm;
     return JNI_VERSION_1_6;
 }

 JNIEnv *GetJNIEnv() {
     JNIEnv *env = NULL;
     if (g_jvm->GetEnv((void **)&env, JNI_VERSION_1_6) != JNI_OK) {
        return NULL;
     }
    return env;
 }


 JNIEXPORT void JNICALL                                                                                    
 Java_com_example_MyClass_nativeMethod(JNIEnv *env, jobject obj) {                                         
     // 将 Java 层对象转成全局引用                                                                         
     if (g_cls == NULL) {                                                                                  
         g_cls = env->GetObjectClass(obj);                                                                 
         g_callbackMethodID = env->GetMethodID(g_cls, "callback", "(Ljava/lang/String;)V");                
         g_obj = env->NewGlobalRef(obj);                                                                   
     }                                                                                                     
                                                                                                           
     // 处理数据                                                                                           
     ...                                                                                                   
     // 回调 Java 方法                                                                                     
     std::thread([=](){                                                                                    
         JNIEnv *env;                                                                                      
         if (g_VM->GetEnv((void **)&env, JNI_VERSION_1_6) == JNI_OK) {                                     
             jstring data = env->NewStringUTF("Hello Java");                                               
             env->CallVoidMethod(g_obj, g_callbackMethodID, data);                                         
             env->DeleteLocalRef(data);                                                                    
         }                                                                                                 
     }).detach();                                                                                          
 }                                                                                                         
                                                                                                           
 JNIEXPORT void JNICALL                                                                                    
 Java_com_example_MyClass_nativeRelease(JNIEnv *env, jobject obj) {                                        
     // 删除全局引用                                                                                       
     env->DeleteGlobalRef(obj);                                                                            
 } 
```
> 这个实现中，还提供了释放全局引用的方法 nativeRelease，以避免内存泄漏。

3 在 Java 层创建对象，并调用 Native 方法。例如：
```java
public class Main {                                                                                       
    public void run() {                                                                                   
        // 创建 Native 对象，并调用 Native 方法                                                           
        MyClass obj = new MyClass();                                                                      
        obj.nativeMethod();                                                                               
    }

     // 该方法将被 Native 层回调                                                                           
     public void callback(String data) {                                                                   
         // 处理 Native 层返回的数据                                                                       
     }                                                                                                     
}   
```


### 其他重点内容

#### JNI中JavaVM（重要）
- 一个进程只有一个 JavaVM。 
- 所有的线程共用一个 JavaVM。

#### JNIEnv
- JNIEnv表示Java调用native语言的环境，封装了几乎全部 JNI 方法的指针。
- JNIEnv只在创建它的线程生效，不能跨线程传递，不同线程的JNIEnv彼此独立。（重要！）
- 在 native 环境下创建的线程，要想和 java 通信，即需要获取一个 JNIEnv 对象。如下：
>1 GetEnv 函数：在某个线程中获取与 JavaVM 绑定的 JNIEnv 对象，以便进行 Java 对象的操作。                   
  2 AttachCurrentThread 函数：将另一个线程附加到 JavaVM 中，并返回与之绑定的 JNIEnv 对象。                  
  3 DetachCurrentThread 函数：将一个线程从 JavaVM 中分离并释放与之绑定的 JNIEnv 对象。                      
  4 GetJavaVM 函数：从 JNI 掉用中获取 JavaVM 对象。
```c++
 static JavaVM *g_jvm = NULL;                                                                              
                                                                                                           
 JNIEXPORT jint JNICALL JNI_OnLoad(JavaVM *vm, void *reserved) {                                           
     if (vm->GetEnv((void **)&g_jni_env, JNI_VERSION_1_6) != JNI_OK) {                                     
         return JNI_ERR;                                                                                   
     }                                                                                                     
     g_jvm = vm;                                                                                           
     return JNI_VERSION_1_6;                                                                               
 }                                                                                                         
                                                                                                           
 JNIEnv *GetJNIEnv() {                                                                                     
     JNIEnv *env = NULL;                                                                                   
     if (g_jvm->GetEnv((void **)&env, JNI_VERSION_1_6) != JNI_OK) {                                        
         return NULL;                                                                                      
     }                                                                                                     
     return env;                                                                                           
 }  
```