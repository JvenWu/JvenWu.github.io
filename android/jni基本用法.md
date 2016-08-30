## forAndroid: JNI Base | JNI基本用法
>环境搭建  
>在Java文件中调用jni函数  
>在 C 文件中定义供 Java 调用的函数

#### 环境搭建
当编译so库时直接用ndk-build命令，可能会出现如下问题

1.问题一：Android NDK: Could not find application project directory  
解决：  
需要注意三个事情  
NDK_PROJECT_PATH - 项目所在的位置  
NDK_APPLICATION_MK - Application.mk 文件的路径  
APP_BUILD_SCRIPT - Android.mk 文件的路径

应当使用如下 ndk-build 命令  
ndk-build NDK_PROJECT_PATH=/path/to/proj NDK_APPLICATION_MK=/path/to/Application.mk

在 Application.mk 文件中添加  
APP_BUILD_SCRIPT := /path/to/Android.mk

若Application.mk 文件和 Android.mk 文件是同级的，则  
APP_BUILD_SCRIPT := Android.mk

此时编译命令为：  
ndk-build NDK_PROJECT_PATH=. NDK_APPLICATION_MK=Application.mk

如果文件都放到jni目录下，则直接 ndk-build 即可

可以把 ndk-build 配置到环境变量中，这样在什么地方都可以直接调用命令

2.问题二：在 AS 中 .so 库放在 libs 文件下而没有放在 jniLibs 文件下，会导致找不到动态库  
解决：  
在 build.gradle 中重定向 jniLibs 文件夹路径到 libs 中
```
android {
    ...
    sourceSets {
        main {
            jniLibs.srcDirs = ['src/main/libs']
        }
    }
}
```

#### 在Java文件中调用jni函数
须先加载动态库、并用native声明方法

1.加载动态库
```java
static {
	System.loadLibrary("hello-jni");
}
```

2.用native声明方法
```java
public native String  stringFromJNI();
```

#### 在 C 文件中定义供 Java 调用的函数
一般函数定义、使用JNI_OnLoad初始化来指定函数替换

1.一般函数定义  
函数名形式为：Java + [包名] + [Java类名] + [方法名]
```c
jstring
Java_net_kgo_jvennote_FFmpegDemoActivity_stringFromJNI( JNIEnv* env,
                                                  jobject thiz )
```

2.使用JNI_OnLoad初始化  
JNI_OnLoad可以和JNIEnv的registerNatives函数结合起来，实现动态的函数替换。  
当在系统中调用System.loadLibrary函数时，该函数会找到对应的动态库，  
然后首先试图找到"JNI_OnLoad"函数，如果该函数存在，则调用它。
```c
//C函数定义
jstring native_funccc(JNIEnv *env, jobject thiz)
{
    return (*env)->NewStringUTF(env,"--- funccc called in version 2\n");
}

//定义批量注册的数组，是注册的关键部分
// func2是在java中声明的native函数名，"()Ljava/lang/String;"是函数的签名，可以通过javah获取。
static const JNINativeMethod gMethods[] = {
        {"func2", "()Ljava/lang/String;", (void *) native_funccc}
};

//这是JNI_OnLoad的声明，必须按照这样的方式声明
jint JNI_OnLoad(JavaVM * vm, void *reserved)
{
    JNIEnv *env = NULL; //注册时在JNIEnv中实现的，所以必须首先获取它
    jint result = -1;

    //从JavaVM获取JNIEnv，一般使用1.4的版本
    if((*vm)->GetEnv(vm, (void**)&env, JNI_VERSION_1_4) != JNI_OK){
        return -1;
    }

    jclass clazz;
    //指定Java中的类名，格式为 【包名】+【类名】
    static const char *const kClassName = "net/kgo/jvennote/FFmpegDemoActivity";

    //这里可以找到要注册的类，前提是这个类已经加载到java虚拟机中。
    //这里说明，动态库和有native方法的类之间，没有任何对应关系。
    clazz = (*env)->FindClass(env, kClassName);

    if(clazz == NULL){
        printf("cannot get class:%s\n", kClassName);
        return -1;
    }

    //这是关键，把本地函数和一个java类方法关联起来。不管之前是否关联过，一律把之前的替换掉！
    if((*env)->RegisterNatives(env, clazz, gMethods,sizeof(gMethods)/sizeof(gMethods[0]))!= JNI_OK)
    {
        printf("register native method failed!\n");
        return -1;
    }

    //重要！必须返回版本，否则加载会失败。
    return JNI_VERSION_1_4;
}
```

#### 示例
**hello-jni.c**

```c
#include <string.h>
#include <stdio.h>
#include <jni.h>

/* This is a trivial JNI example where we use a native method
 * to return a new VM String. See the corresponding Java source
 * file located at:
 *
 *   net/kgo/jvennote/FFmpegDemoActivity.java
 */
jstring
Java_net_kgo_jvennote_FFmpegDemoActivity_stringFromJNI(JNIEnv *env,
                                                       jobject thiz) {
#if defined(__arm__)
#if defined(__ARM_ARCH_7A__)
#if defined(__ARM_NEON__)
#if defined(__ARM_PCS_VFP)
#define ABI "armeabi-v7a/NEON (hard-float)"
#else
#define ABI "armeabi-v7a/NEON"
#endif
#else
#if defined(__ARM_PCS_VFP)
#define ABI "armeabi-v7a (hard-float)"
#else
#define ABI "armeabi-v7a"
#endif
#endif
#else
#define ABI "armeabi"
#endif
#elif defined(__i386__)
#define ABI "x86"
#elif defined(__x86_64__)
#define ABI "x86_64"
#elif defined(__mips64)  /* mips64el-* toolchain defines __mips__ too */
#define ABI "mips64"
#elif defined(__mips__)
#define ABI "mips"
#elif defined(__aarch64__)
#define ABI "arm64-v8a"
#else
#define ABI "unknown"
#endif

    return (*env)->NewStringUTF(env, "Hello from JNI !  Compiled with ABI " ABI ".");
}

//C函数定义
jstring native_funccc(JNIEnv *env, jobject thiz)
{
    return (*env)->NewStringUTF(env,"--- funccc called in version 2\n");
}

//定义批量注册的数组，是注册的关键部分
// func2是在java中声明的native函数名，"()Ljava/lang/String;"是函数的签名，可以通过javah获取。
static const JNINativeMethod gMethods[] = {
        {"func2", "()Ljava/lang/String;", (void *) native_funccc}
};

//这是JNI_OnLoad的声明，必须按照这样的方式声明
jint JNI_OnLoad(JavaVM * vm, void *reserved)
{
    JNIEnv *env = NULL; //注册时在JNIEnv中实现的，所以必须首先获取它
    jint result = -1;

    //从JavaVM获取JNIEnv，一般使用1.4的版本
    if((*vm)->GetEnv(vm, (void**)&env, JNI_VERSION_1_4) != JNI_OK){
        return -1;
    }

    jclass clazz;
    //指定Java中的类名，格式为 【包名】+【类名】
    static const char *const kClassName = "net/kgo/jvennote/FFmpegDemoActivity";

    //这里可以找到要注册的类，前提是这个类已经加载到java虚拟机中。
    //这里说明，动态库和有native方法的类之间，没有任何对应关系。
    clazz = (*env)->FindClass(env, kClassName);

    if(clazz == NULL){
        printf("cannot get class:%s\n", kClassName);
        return -1;
    }

    //这是关键，把本地函数和一个java类方法关联起来。不管之前是否关联过，一律把之前的替换掉！
    if((*env)->RegisterNatives(env, clazz, gMethods,sizeof(gMethods)/sizeof(gMethods[0]))!= JNI_OK)
    {
        printf("register native method failed!\n");
        return -1;
    }

    //重要！必须返回版本，否则加载会失败。
    return JNI_VERSION_1_4;
}
```

**FFmpegDemoActivity.java**
```java
public class FFmpegDemoActivity extends BaseActivity {
    @Override
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);

        /* Create a TextView and set its content.
         * the text is retrieved by calling a native
         * function.
         */
        TextView  tv = new TextView(this);
        tv.setText( stringFromJNI() + func2());
        setContentView(tv);
        Log.d("native",func2());
    }

    /* A native method that is implemented by the
     * 'hello-jni' native library, which is packaged
     * with this application.
     */
    public native String  stringFromJNI();

    /*一个本地方法，用于演示
     * JNI_OnLoad 可以和 JNIEnv 的 registerNatives 函数结合起来实现动态的函数替换
     */
    public native String func2();

    /* This is another native method declaration that is *not*
     * implemented by 'hello-jni'. This is simply to show that
     * you can declare as many native methods in your Java code
     * as you want, their implementation is searched in the
     * currently loaded native libraries only the first time
     * you call them.
     *
     * Trying to call this function will result in a
     * java.lang.UnsatisfiedLinkError exception !
     */

     public native String  unimplementedStringFromJNI();

    /* this is used to load the 'hello-jni' library on application
     * startup. The library has already been unpacked into
     * /data/data/net.kgo.jvennote/lib/libhello-jni.so at
     * installation time by the package manager.
     */
    static {
        System.loadLibrary("hello-jni");
    }
}
```
