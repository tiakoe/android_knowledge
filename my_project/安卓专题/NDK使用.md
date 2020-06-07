



### 使用方式

> 配置 Android NDK环境（除了配置好NDK外，还需在SDK Manager中下载CMake）
> 创建 Android 项目，与 NDK进行关联（创建项目时选择C++ support)
> 在 Android 项目中声明所需要调用的 Native方法
> 用Native语言实现在Android中声明的Native方法
> 通过 ndk-bulid 命令编译产生.so库文件



### 配置

在`local.properties`中加入

```
ndk.dir=  <ndk路径>
```

旧版本的NDK支持

```
android.useDeprecatedNdk=true
```



    defaultConfig {
        ......
        externalNativeBuild {
            cmake {
                cppFlags ""
            }
        }
    }
    ......
    externalNativeBuild {
        cmake {
            path "CMakeLists.txt"
        }
    }



# 开发Native代码

### 在Java文件中声明native方法

首先需要在Java代码的类中通过static块来加载我们的Native库。

```java
static {
	System.loadLibrary("native-lib");  //loadLibrary的参数是在CMakeList.txt中定义的Native库的名称
}
//之后，我们便可以在这个类中声明Native方法
public native String getStringFromJNI();
```

### 创建CMakeList.txt

在src中创建一个CMakeList.txt文件，这个文件约束了Native语言源文件的编译规则

```txt
cmake_minimum_required(VERSION 3.4.1)

add_library(native-lib SHARED src/main/cpp/native-lib.cpp)

find_library(log-lib log)

target_link_libraries(native-lib ${log-lib})
```

add_library方法中定义了一个so库，它的名称是native-lib，也就是我们在Java文件中用到的字符串，而后面则跟着这个库对应的Native文件的路径

find_library则是定义了一个路径变量，经过了这个方法，log-lib这个变量中的值就是Android中log库的路径

target_link_libraries则是将native-lib这个库和log库连接了起来，这样我们就能在native-lib中使用log库的方法。


### 创建Native方法文件

在src/main/cpp/创建native-lib.cpp

```c++
#include <jni.h>
#include <string>

extern "C"{
  JNIEXPORT jstring JNICALL
  Java_com_n0texpecterr0r_ndkdemo_MainActivity_getStringFromJNI(
      JNIEnv* env,
      jobject) {
    std::string hello = "IG牛逼";
    return env->NewStringUTF(hello.c_str());
  }
}
```





### 方法签名

#### 概念

在我们JNI层调用一个方法时，需要传递一个参数——方法签名。因为在Java中的方法是可以重载的，两个方法可能名称相同而参数不同。为了区分调用的方法，就引入了方法签名的概念。



> ### 签名规则
>
> 对于基本类型的参数，每个类型对应了一个不同的字母：
>
> boolean Z
> byte B
> char C
> short S
> int I
> long J
> float F
> double D
> void V
> 对于类，则使用 L+类名 的方式，其中(.)用(/)代替，最后加上分号
>
> 比如 java.lang.String就是 Ljava/lang/String;
>
> 对于数组，则在前面加 [ ，然后加类型的签名，几维数组就加几个。
>
> 比如 int[]对应的就是[I ， boolean[][]对应的则是[[Z，而java.lang.String[]就是[Ljava/lang/String;



#### 打印方法签名

我们可以通过 `javap -s` 命令来打印方法的签名。

例子
比如下面的方法

public native String getMessage();

public native String getMessage(String id,long i);

对应的方法签名分别为：

()Ljava/lang/String;
(Ljava/long/String;J)Ljava/lang/String;



### 调用java静态方法

```
在JNI中，其实也是一致的：

我们先需要同JVM环境的函数找到对应的jclass，对应Java中的类。
拿到了对应的类之后，我们可以通过JVM环境中的函数拿到需要调用的方法的jmethodID类型的变量。
获取到jmethodID之后，我们就可以调用JVM环境中的对应函数调用对应方法来进行函数的调用。
调用完成后，释放相关资源即可。
```



##### 具体过程

在MainActivity定义了如下两个方法:

```java
public static void logMessage(String msg){
    Log.d("NDK", msg);
}
public native void callStaticMethod();
```



为native方法生成了如下的C++代码:

```c++
JNIEXPORT void JNICALL
Java_com_n0texpecterr0r_ndkdemo_MainActivity_callStaticMethod(JNIEnv *env, jobject instance) {

}
```

 

通过env的`FindClass`方法找到对应的类:

```
jclass cls_main = env->FindClass("com/n0texpecterr0r/ndkdemo/MainActivity");
```

FindClass需要的参数是这个类的全路径。获取到的jclass我们最好判下空

接下来我们需要拿到我们要调用的方法对应的jmethodID。

```
jmethodID mth_static_method = env->GetMethodID(cls_main,"logMessage","(Ljava/lang/String;)V");
```

 

完整代码：

```
JNIEXPORT void JNICALL
Java_com_n0texpecterr0r_ndkdemo_MainActivity_callStaticMethod(JNIEnv *env, jobject instance) {
  // 找到对应的类
  jclass cls_main = env->FindClass("com/n0texpecterr0r/ndkdemo/MainActivity");
  if(cls_main == NULL)  return;
  // 获取methodId
  jmethodID mth_static_method = env->GetStaticMethodID(cls_main,"logMessage","(Ljava/lang/String;)V");
  if(mth_static_method == NULL) return;
  // 构建String变量
  jstring str = env->NewStringUTF("这是从JNI调用的Log");
  // 调用static方法
  env->CallStaticVoidMethod(cls_main,mth_static_method,str);
  // 释放内存
  env->DeleteLocalRef(cls_main);
  env->DeleteLocalRef(str);
}
```

有了之前的jclass之后，我们还可以通过获取`jfieldID`,之后调用env的`SetStaticObjectField`方法来修改static变量。与前面类似。



### 参考文献：

https://blog.csdn.net/qq_21556263/article/details/84144396





## 个人总结：

gradle中配置cpp路劲，在java代码中写明方法，cpp中写明实现。在cpp中的方法对应java的方法，大多数是在java的关键字前加j，然后方法为路径，将. 改为_   ,参数主要包含jni环境，jni对象。一切皆对应java方法。

格式：`Java_包名_类名_需要调用的方法名`

其中，Java必须大写，包名里的`.`要改成`_`，`_`要改成`_1`

- JNIEnv* env：代表了JVM的环境，Native方法可以通过这个指针来调用Java代码
- jobject obj：它就相当于定义了这个JNI方法的类 (MainActivity) 的this引用

java中在静态代码块中写库的加载，然后native替代方法的static



注意格式：

```
extern "C"
JNIEXPORT jstring JNICALL
```

**先在java中写方法，然后点击提示，自动生成JNI的函数模块**

```c++
#include <jni.h>
#include <string>

//在C中不支持重载，不同参数的函数签名一样，签名包含函数名称
//在C++中支持重载，签名包含函数名称、函数参数、返回类型
//C的函数库给C++调用，需要extern “C”来告诉编译器使用编译C的方式来连接

extern "C" 
JNIEXPORT jstring JNICALL
Java_com_rustfisher_fishpole_worker_FisherTom_name(JNIEnv *env, jobject thiz) {
    std::string myName = "Tom";
    return env->NewStringUTF(myName.c_str());
}
```

```java
public class FisherTom {
    static {
        System.loadLibrary("fisher-pole");
    }

    public native int addFish(int a, int b);

    public native float calFish(float f1, float f2);

    public native String name();
}
```



JNIEXPORT和JNICALL关键字，这两个关键字是两个宏定义，他主要的作用就是说明该函数为JNI函数

而jstring则对应了Java中的String类，JNI中有很多类似jstring的类来对应Java中的类，下面是Java中的类与JNI类型的对照表

基本数据类型：除了void类型，其他java类型前加个j变为JNI类型

如：jobject、jclass、jstring、

数组类型：同基本数据类型转换类似，先在前面加个j，然后在后面加个Array

如：jintArray、jbooleanArray



数据类型签名（标识java类型）
除了boolean为大写的Z、long为大写的J，其他均为首字母大写



## 调用java代码

JNI与java的交互，用JNI代码调用java代码，java中存在重载，于是调用时引入签名概念

**八种基本数据类型+一个void，除了boolean为Z，long为J，其余均为首字母大写**

类用**L+类名** 的方式，其中(.)用(/)代替，最后加上分号

比如 java.lang.String就是 Ljava/lang/String;

对于数组，则在前面加 [ ，然后加类型的签名，几维数组就加几个。

比如 `int[]`对应的就是`[I` 

 `boolean[][]`对应的则是`[[Z`

`java.lang.String[]`就是`[Ljava/lang/String;`

举例：

public native String getMessage(); 

public native String getMessage(String id,long i);

结果：

()Ljava/lang/String;

(Ljava/lang/String;J)Ljava/lang/String;

- 可以通过 `javap -s` 命令来打印方法的签名




调用java静态方法例子：

```c++
JNIEXPORT void JNICALL
Java_com_n0texpecterr0r_ndkdemo_MainActivity_callStaticMethod(JNIEnv *env, jobject instance) {
  // 找到对应的类
  jclass cls_main = env->FindClass("com/n0texpecterr0r/ndkdemo/MainActivity");
  if(cls_main == NULL)  return;
  // 获取methodId
  jmethodID mth_static_method = env->GetStaticMethodID(cls_main,"logMessage","(Ljava/lang/String;)V");
  if(mth_static_method == NULL) return;
  // 构建String变量
  jstring str = env->NewStringUTF("这是从JNI调用的Log");
  // 调用static方法
  env->CallStaticVoidMethod(cls_main,mth_static_method,str);
  // 释放内存
  env->DeleteLocalRef(cls_main);
  env->DeleteLocalRef(str);
}
```



调用java实例对象的方法例子：

```c++
JNIEXPORT jint JNICALL
Java_com_n0texpecterr0r_ndkdemo_MainActivity_addNative(JNIEnv *env, jobject instance, jint arg1, jint arg2) {
  // 找到对应类
  jclass cls_adder = env->FindClass("com/n0texpecterr0r/ndkdemo/Adder");
  // 获取构造方法,   在获取构造函数的id时，指定的方法名为<init>
  jmethodID mth_constructor = env->GetMethodID(cls_adder,"<init>","(II)V");
  // 调用构造方法构建jobject
  jobject adder = env->NewObject(cls_adder, mth_constructor,arg1,arg2);
  // 获取add方法
  jmethodID mth_add = env->GetMethodID(cls_adder,"doAdd","()I");
  // 调用add方法获取返回值
  jint result = env->CallIntMethod(adder,mth_add);
  // 回收资源
  env->DeleteLocalRef(cls_adder);
  env->DeleteLocalRef(adder);
  // 返回结果
  return result;
}
```



## 对象的释放

对象传递给Native语言后，Native层会持有Java对象，如果我们不妥善处理会导致内存泄漏。 

因此在Native层使用Java对象时，需要释放这个引用。



## 动态注册

Java类是通过VM来调用Native方法，调用时需要通过VM在so库中寻找Native函数，如果该函数需频繁调用，会有大量的时间消耗。因此我们可以通过动态注册，在JNI_Onload函数中把**native函数注册到VM中**，减少寻找花费的时间。

在调用`System.loadLibrary()`方法时，会自动在该库中查找并调用一个叫JNI_Onload的函数。

```
JNIEXPORT jint JNICALL JNI_OnLoad(JavaVM* vm, void* reserved) {
	...
}
```

由于这个函数是在JNI被加载时调用，所以它有如下的几个用途:

- 在JNI_Onload通知VM当前so库使用的JNI版本(默认会返回最老的版本1.1)
- 在JNI_Onload中进行数据的初始化
- 在JNI_Onload中对Java类中的Native方法进行动态注册。



Android系统加载JNI依赖库的方式有下面的两种：

- 如果JNI_Lib定义了JNI_Onload函数，则通过JNI_Onload函数
- 如果JNI_Lib没有定义JNI_Onload函数，则dvm调用`dvmResolveNativeMethod`进行动态解析。
  

比如假设我们有如下的一个静态注册的Native文件

```
#include <string>  
#include <jni.h>  
  
extern "C"
JNIEXPORT jstring JNICALL  Java_com_example_hellojni_HelloJni_stringFromJNI(JNIEnv* env, jobject thiz )  
{  
    return env->NewStringUTF("Hello from JNI !");  
}  
```

将其改写为如下的方式，即可完成方法的动态注册

```
extern "C"
jstring native_hello(JNIEnv* env, jobject thiz )  
{  
    return env->NewStringUTF("Hello from JNI !");  
}  

JNINativeMethod gMethods[] = {  
    {"stringFromJNI", "()Ljava/lang/String;", (void*)native_hello},//绑定  
};  
  
int registerNativeMethods(JNIEnv* env, const char* className, JNINativeMethod* gMethods, int numMethods) {  
    jclass clazz;  
    clazz = env->FindClass(className);  
    if (clazz == NULL) {  
        return JNI_FALSE;  
    }  
    if (env->RegisterNatives(clazz, gMethods, numMethods) < 0) {  
        return JNI_FALSE;  
    }  
  
    return JNI_TRUE;  
}  
  
  
/* 
* 为所有类注册本地方法 
*/  
int registerNatives(JNIEnv* env) {  
    const char* kClassName = "com/example/hellojni/HelloJni";//指定要注册的类  
    return registerNativeMethods(env, kClassName, gMethods,  
            sizeof(gMethods) / sizeof(gMethods[0]));  
}  
  
/* 
* System.loadLibrary("lib")时调用 
* 如果成功返回JNI版本, 失败返回-1 
*/  
extern "C"
JNIEXPORT jint JNICALL JNI_OnLoad(JavaVM* vm, void* reserved) {  
    JNIEnv* env = NULL;  
    jint result = -1;  
  
    if (vm->GetEnv((void**) &env, JNI_VERSION_1_4) != JNI_OK) {  
        return -1;  
    }  
    assert(env != NULL);  
  
    if (!registerNatives(env)) {//注册  
        return -1;  
    }  
    //成功  
    result = JNI_VERSION_1_4;  
  
    return result;  
}  
```

在上述方法中，我们创建了一个方法对应表，将Java中的Native方法与Native层中的函数一一对应。

其中，`JNINativeMethod`是一个JNI中定义的结构体

```
typedef struct {    
    const char* name;  		
    const char* signature;  
    void* fnPtr;  			
} JNINativeMethod; 
```

name代表了Java中的函数的名字，而signature代表了这个函数的函数签名，fnPtr则是一个函数指针，指向一个C函数，也就是native方法对应的函数。



## .so文件

我们都知道，在项目中使用NDK时，它会生成so文件供我们的Java代码调用。我们使用某些Java语言编写的库时，它可能内部也用到了.so文件。

so是shared object的缩写，也就是共享对象的意思。它是一种二进制文件，里面存放的是机器可以直接运行的二进制代码。正是因为如此，所以反编译so库的难度会比反编译普通Java代码的难度更大。so主要应用在Unix和Linux操作系统中，大到操作系统，小到一个专用的软件，都离不开so。其实Windows中有和so类似的东西，也就是我们常见的dll(动态链接库)，其实它们是相同的事物，只是名字不同而已。


#### 使用so的好处

- 让开发者最大化利用已有的C和C++代码，达到重用的效果，利用软件世界积累了几十年的优秀代码
- 二进制，没有解释编译的开销，实现同样的功能比纯粹Java实现要快
- 内存不受VM单个应用限制，可以减少OOM。



JNI是JAVA NATIVE INTERFACE， JNI只是一个中间层。JNI中要用c/c++作具体实现。达到c/c++与Java通信的作用。不一定要编译成so文件，才叫JNI开发。也不是Android上才有，Java程序员一样可以做JNI，不会写Activity也可以。Java最简单Main函数，可以声明一个Native方法，在VS中，写一段c/c++代码，按照JNI规范。编译成dll文件，放到Java工程中，一样可以执行。而so是只是不同平台动态链接库的产物，因为Android是基于linux平台。







### 精选文章：

https://blog.csdn.net/qq_21556263/article/details/84144396



库文件一般指计算机上的一类文件，分两种，一种是静态库，另一种是动态库即DLL(Dynamic Link Library)文件，又叫动态链接库文件。静态库和动态库的区别是：静态库在程序的链接阶段被复制到了程序中；动态库在链接阶段没有被复制到程序中，而是程序在运行时由系统动态加载到内存中供程序调用。使用动态库的优点是系统只需载入一次动态库，不同的程序可以得到内存中相同的动态库的副本，因此节省了很多内存，而且使用动态库也便于模块化更新程序。

一个应用程序可使用多个DLL文件，一个DLL文件也可能被不同的应用程序使用，这样的DLL文件被称为共享DLL文件。



### 哪些场景下要用到NDK开发？

>  跨平台的库，如FFmpeg, skip，weex,
>  加固，防逆向
>  签名校验
>  图片压缩
>  音视频解码
>  OpenGL ES 高级特效
>  热修复 andfix
>  人脸识别 face++
>  加密算法
>  微信apk中的so文件



https://github.com/ganlvtech/phaser-catch-the-cat



## 报错

```
ABIs [arm64-v8a,armeabi-v7a,armeabi] set by 'android.injected.build.abi' gradle flag contained 'ARMEABI' not targeted by this project.
```

在gradle.properties添加： android.useDeprecatedNdk=true









