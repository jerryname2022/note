# configure配置交叉编译环境
## 1.设置gcc等工具的环境
- 系统库路径。 --sysroot= 选项设置 //eg: --sysroot=$NDK\platforms\android-9\arch-arm
- gcc前缀设置。--cross-prefix=  选项设置 //eg:  --cross-prefix=$NDK/toolchains/x86-4.9/prebuilt/linux-x86_64/bin/i686-linux-android-
- gcc的运行主机。--host=    选项设置  //eg: --host=aarch64-linux-android
- gcc附加编译参数。--extra-cflags="" --extra-ldflags="" 选项设置。 cflags可以添加gcc头文件搜索路径。ldflags可以添加库文件搜索路
## 2.库和头文件安装路径
- --prefix= 设置。
- eg: --prefix=`pwd`/android/arm
## 3.库的剪裁选项
- eg: --enable-shared --enable-pic
### eg:
NDK=$ANDROID_NDK \
SYSROOT=$NDK/platforms/android-9/arch-x86/ \
TOOLCHAIN=$NDK/toolchains/x86-4.9/prebuilt/linux-x86_64 \
function build_x86 \
{ \
./configure \
--prefix=$PREFIX \
--enable-shared \
--enable-static \
--enable-strip \
--enable-cli \
--enable-pic \
--enable-cross-compile \
--cross-prefix=$TOOLCHAIN/bin/i686-linux-android- \
--host=i686-linux-android \
--sysroot=$SYSROOT \
--extra-cflags="-Os -fpic $ADDI_CFLAGS" \
--extra-ldflags="$ADDI_LDFLAGS" \
$ADDITIONAL_CONFIGURE_FLAG \
} \
CPU=x86 \
PREFIX=$(pwd)/android/$CPU \
ADDI_CFLAGS="" \
build_x86
