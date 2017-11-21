# 三种方式使用ndk进行编译
## 1.gcc 的sysroot 选项
- sysroot 选项设定 gcc 在编译源码的时候，寻找头文件和库文件的根目录。可以这样调用 gcc --sysroot=/tmp/gcc-arm (及其他选项)。
- NDK 根目录下的 platforms 目录中的各个子目录的路径都可以直接传给 gcc --sysroot=<dir>。
为了简化操作，可以在linux系统的命令终端执行以下命令，设置SYSROOT环境变量，$NDK是ndk的根目录。
- 设置 SYSROOT之后，要把它传给 gcc 的 --sysroot 选项。由于unix/linux自带的gcc并非交叉编译工具，而我们需要使用的是ndk中提供的交叉编译工具（也是gcc），所以需要想办法让编译脚本找到ndk中的gcc，而不要去寻找系统中的gcc。
- 而 unix/linux 系统的编译脚本常常会用 CC 环境变量来引用编译器，所以通过把 CC 设置为ndk中的gcc的路径，就能帮助编译脚本找到正确的gcc（我们还能顺便加上--sysroot选项）。
- 将CC 按如下设置
$  export CC="$NDK/toolchains/<name>/prebuilt/<host-system>/bin/<prefix>gcc --sysroot=$SYSROOT"
$ $CC -o foo.o -c foo.c  (不必执行这一行，这条命令是调用gcc编译程序）
- 上面第1行之后之后，再去执行./configure 就可以编译出arm程序了。
- 不过还需要考虑共享库的链接问题，要确保该程序没有链接ndk未提供的共享库。
该方法的缺陷就是，不能使用 C++ STL（STLport 或 GNU libstdc++ ），也不能使用异常机制和RTTI。

## 2.调用NDK编译器
- android ndk 提供脚本，允许自己定制一套工具链。例如：
$ $NDK/build/tools/make-standalone-toolchain.sh --platform=android-5 --install-dir=/tmp/my-android-toolchain [ --arch=x86 ]
- 将会在/tmp/my-android-toolchain 中创建 sysroot 环境和 工具链。--arch 选项选择目标程序的指令架构，默认是为 arm。
- (执行 make-standalone-toolchain.sh --help 查看帮助。)
- 运行之后，这样使用：
$ export PATH=/tmp/my-android-toolchain/bin:$PATH
$ export CC=arm-linux-androideabi-gcc
$ export CXX=arm-linux-androideabi-g++
$ export CXXFLAGS="-lstdc++"
- 执行完以上设置环境变量的命令之后，就可以直接编译了（例如，执行 ./configure 然后 make 得到的就是 arm 程序了）。
- 不用再设定 sysroot, CC 了。而且，可以使用 STL，异常，RTTI。
- ABI 兼容性
- ndk 同时支持 arm5 和 arm7，一般只用 arm5就好了。arm7是高端一点的，NDK 默认也是 arm5 。
- 推荐加上 -mthumb 选项给gcc，来生成 16-bit Thumb-1 指令。
- 如果要用 arm7，可以设定 CFLAGS='-march=armv7-a -mfloat-abi=softfp'， 使用 Thumb-2 指令，且这两个选项不能分开！
## 3.ndk-build 命令
