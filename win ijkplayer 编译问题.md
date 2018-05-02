编译步骤：

```
# 直接拉取源代码到本地
git clone https://github.com/Bilibili/ijkplayer.git ijkplayer-android
cd ijkplayer-android

# 检查更新代码
git checkout -B latest k0.5.1

# 初始化，会把ffmpeg的代码拉取到本地等等操作
./init-android.sh

cd android/contrib
./compile-ffmpeg.sh clean
# 编译ffmpeg软解码库
./compile-ffmpeg.sh all

cd ..
# 会生成各种版本的so文件
./compile-ijk.sh all
```

在执行./compile-ffmpeg.sh all 报错：
问题：
![这里写图片描述](http://img.blog.csdn.net/20161215210623181?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ3FpdXNoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

NDKr10e(64-bit) detected
Host system 'windows' is not supported by the source NDK!
Try --system=<name> with one of:  windows-x86_64

该问题由于make-standalone-toolchain.sh默认为32bit的编译器，当在64bit机器上进行编译就会报该错误，要再make-standalone-toolchain.sh  加上参数 “--system=windows-x86_64”

![来自：“http://blog.csdn.net/dssxk/article/details/50541621”](http://img.blog.csdn.net/20161215211007203?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ3FpdXNoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


```
if [ "$FF_ARCH" = "armv7a" ]; then
    FF_BUILD_NAME=ffmpeg-armv7a
    FF_BUILD_NAME_OPENSSL=openssl-armv7a
    FF_BUILD_NAME_LIBSOXR=libsoxr-armv7a
    FF_SOURCE=$FF_BUILD_ROOT/$FF_BUILD_NAME

    FF_CROSS_PREFIX=arm-linux-androideabi
    FF_TOOLCHAIN_NAME=${FF_CROSS_PREFIX}-${FF_GCC_VER}

    FF_CFG_FLAGS="$FF_CFG_FLAGS --arch=arm --cpu=cortex-a8"
    FF_CFG_FLAGS="$FF_CFG_FLAGS --enable-neon"
    FF_CFG_FLAGS="$FF_CFG_FLAGS --enable-thumb"

    FF_EXTRA_CFLAGS="$FF_EXTRA_CFLAGS -march=armv7-a -mcpu=cortex-a8 -mfpu=neon -mfloat-abi=softfp -mthumb"
    FF_EXTRA_LDFLAGS="$FF_EXTRA_LDFLAGS -Wl,--fix-cortex-a8"

    FF_ASSEMBLER_SUB_DIRS="arm"

elif
```