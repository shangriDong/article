刚毕业的时候弄了一年半的neon，到现在 也忘得差不多了， 最近项目 又要重新 拾起了。特来记录一些问题。之前都是熊瞎子掰苞米了。。。。



```
Error:(12, 0) Error: NDK integration is deprecated in the current plugin.  
Consider trying the new experimental plugin.  
For details, see http://tools.Android.com/tech-docs/new-build-system/gradle-experimental.  
Set “android.useDeprecatedNdk=true” in gradle.properties to continue using the current NDK integration.
```

想新建一个benchmark，在Android Studio上新建一个 工程与之前有些差异上面的问题是刚编译 就报了个错误，查了一下解决办法：
**android.useDeprecatedNdk=true**解决ndk提示版本低不能自动编译jni。在gradle.properties文件中加入这句话就可以了。


编译时又遇到了undefined reference to `__android_log_print 问题。

经最后实验：

```
//1. 
Android.mk->LOCAL_LDLIBS := -llog
//2 
ndk {
    abiFilters "armeabi-v7a"
    ldLibs "log"
}
sourceSets {
    main {
        jni.srcDirs = ['src/main/jni/']
        jniLibs.srcDirs = ['src/main/libs']
    }
}
//3 cpp文件要包含以下内容：
#include <android/log.h>

#define TAG    "Main_jni"
#define LOGD(...)  __android_log_print(ANDROID_LOG_DEBUG,TAG,__VA_ARGS__)
```
才最终解决。


----------


想引入

```
#include <iostream>
```
一直报下面的错误：

```
error: iostream: No such file or directory
```

查了很多文档，都是如下解释： 在Application.mk中加入

```
APP_STL := stlport_static
APP_ABI := armeabi-v7a
```

引入stlport_static，但是仍然不可以。最后我发现是:

```
sourceSets {
    main {
        jni.srcDirs = ['src/main/jni/']
        jniLibs.srcDirs = ['src/main/libs']
    }
}
```
jni.srcDirs = ['src/main/jni/']惹得祸。。。会把所有jni下面的文件参与编译，改为jni.srcDirs = []。在android.mk里面进行配置要编译的文件即可。


----------
