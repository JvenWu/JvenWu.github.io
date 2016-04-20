## for Android: Install Android IN Ubuntu | 在Ubuntu上安装Android源码
>*not test below*

##安装git工具：sudo apt-get install git-core gnupg

##从命令行安装jdk
sudo add-apt-repository ppa:ferramroberto/java

sudo apt-get update

sudo apt-get install sun-java6-jre sun-java6-plugin

sudo apt-get install sun-java6-jdk

##下载jdk文件安装方式
1.官网下载jdk解压缩到/usr/lib/jvm，解压时要用root权限
sudo tar -xzvf jdk-8u65-linux-x64.tar.gz -C /usr/lib/jvm 

2.cd进/usr/lib/jvm 查看是否有 jdk1.8.0_65文件夹

3.设置java环境变量

/etc/profile的设置方法对所有登录的用户都有效。
~/.bashrc只对当前用户有效。

上面两个都是配置文件，开机后，系统会先读取/etc/profile，再读~/.bashrc。
不同的用户~/.bashrc文件可以有不同的设置，而/etc/profile则是共用一个，只有root才能修改。
~/.bashrc对/etc/profile有追加覆盖的效果。

在/etc/profile中添加：

export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_65

export JRE_HOME=${JAVA_HOME}/jre

export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib

export PATH=${JAVA_HOME}/bin:$PATH

设置完后重启下

4.检测java是否设置成功

java -version

5.检测java是否可以编译

新建java源程序HelloWorld.java
```java
public class HelloWorld{
	public static void main(String[] arg){
		System.out.println("hello world");
	}
}
```
输入命令

javac HelloWorld.java

java HelloWorld


##安装其它包: sudo apt-get install flex bison gperf libsdl-dev libesd0-dev libwxgtk2.6-dev build-essential zip curl

##安装调试工具: sudo apt-get install valgrind

##在用户目录下，创建bin文件夹，用于存放repo，并把该路径设置到环境变量中
> mkdir ~/bin  
PATH=~/bin:$PATH 

##下载repo的脚本，用于执行repo:  
> 下载repo脚本方式一  
wget https://dl-ssl.google.com/dl/googlesource/git-repo/repo  
chmod 777 repo  
cp repo /bin/

> 下载repo脚本方式二  
curl https://dl-ssl.google.com/dl/googlesource/git-repo/repo > ~/bin/repo  
$ chmod a+x ~/bin/repo 

##下载安卓工程  
> mkdir Android  
cd Android

> 默认下载主线分支  
repo init -u https://dl-ssl.google.com/dl/googlesource/git-repo/repo 

> 下载主线分支，主分支里面包含着最新修改的bugs，并未正式发出版本的最新源码  
repo init -u https://android.googlesource.com/platform/manifest  

> 下载其他分支，正式发布的版本，可以通过添加-b的命令下载下来  
repo init -u https://android.googlesource.com/platform/manifest -b android-4.0.1_r1  

> 同步代码  
repo sync

##代码下载过程中，提示错误解决方法
> 编辑/etc/hosts文件  
$ vim /etc/hosts  
增加下面内容，保存（提前保存好）：  
74.125.31.82 www.googlesource.com  
74.125.31.82 android.googlesource.com  
203.208.46.172 cache.pack.google.com  
59.24.3.173 cache.pack.google.com  
然后重新输入  
$ repo sync

##编译安装源码：在Android目录下 make

##编译安装源码：在Android目录下 make sdk

##安装编译好的Android镜像到模拟器上
1.设置环境变量

USER-NAME@MACHINE-NAME:~/Android$ export PATH=$PATH:~/Android/out/host/linux-x86/bin  

USER-NAME@MACHINE-NAME:~/Android$ export ANDROID_PRODUCT_OUT=~/Android/out/target/product/generic

其中，~/Android/out/host/linux-x86/bin有我们要执行的emulator命令，

而~/Android/out/target/product/generic是Android镜像存放目录，下面执行emulator命令时会用到。

2.运行模拟器

USER-NAME@MACHINE-NAME:~/Android$ emulator

模拟器运行需要四个文件，分别是Linux Kernel镜像zImage和Android镜像文件system.img、userdata.img和ramdisk.img

3.指定镜像文件来运行模拟器 

USER-NAME@MACHINE-NAME:~/Android$ emulator -kernel ./prebuilt/android-arm/kernel/kernel-qemu -sysdir ./out/target/product/generic -system system.img -data userdata.img -ramdisk ramdisk.img

## 预先编译好的内核位置: prebuilt/android-arm/kernel/kernel-qemu

## 下载Linux Kernel源代码
> 1. 使用GIT下载，执行以下命令  
USER-NAME@MACHINE-NAME:~/Android$ mkdir kernel  
USER-NAME@MACHINE-NAME:~/Android$ cd kernel  
USER-NAME@MACHINE-NAME:~/Android/kernel$ git clone http://android.googlesource.com/kernel/goldfish.git

> 2. 查看下载的内核代码版本(Linux内核代码在common目录中)  
USER-NAME@MACHINE-NAME:~/Android/kernel$ cd common  
USER-NAME@MACHINE-NAME:~/Android/kernel/common$ git branch

> 3. 上面得到的是主线上的内核源代码，现在我们需要适用于模拟器用的内核，需要切换goldfish版本  USER-NAME@MACHINE-NAME:~/Android/kernel/common$ git branch -a  
选择最新的android-goldfish版本  
USER-NAME@MACHINE-NAME:~/Android/kernel/common$ git checkout remotes/origin/archive/android-gldfish-2.6.29

## 编译内核代码。
> 1. 导出交叉编译工具目录到$PATH环境变量  
USER-NAME@MACHINE-NAME:~/Android/kernel/common$ export PATH=$PATH:~/Android/prebuilt/linux-x86/toolchain/arm-eabi-4.4.3/bin

> 2. 修改common目录下的Makefile文件的以下两行为  
\# ARCH ?= (SUBARCH)  
\# CROSS_COMPILE?= 
ARCH ?= arm  #体系结构为arm
CROSS_COMPILE     ?= arm-eabi- #交叉编译工具链前缀，参考~/Android/prebuilt/linux-x86/toolchain/arm-eabi-4.4.3/bin目录 

> 3. 开始编译  
USER-NAME@MACHINE-NAME:~/Android/kernel/common$ make goldfish_defconfig  
USER-NAME@MACHINE-NAME:~/Android/kernel/common$ make  
编译成功后，可看到下面两行：  
OBJCOPY arch/arm/boot/zImage  
Kernel: arch/arm/boot/zImage is ready  
执行make命令前，也可以执行make menuconfig先配置一下编译选项

## 在模拟器中运行编译好的内核
> 1. 在启动模拟器之前，先设置模拟器的目录到环境变量$PATH  
USER-NAME@MACHINE-NAME:~/Android$ export PATH=$PATH:~/Android/out/host/linux-x86/bin

> 2. 设置ANDROID_PRODUCT_OUT环境变量  
USER-NAME@MACHINE-NAME:~/Android$ export ANDROID_PRODUCT_OUT=~/Android/out/target/product/generic

> 3. 在后台中指定内核文件启动模拟器  
USER-NAME@MACHINE-NAME:~/Android$  emulator -kernel ./kernel/common/arch/arm/boot/zImage &

> 4. 用adb工具连接模拟器，查看内核版本信息  
USER-NAME@MACHINE-NAME:~/Android$ adb shell  
root@android:/ # cd proc  
root@android:/proc # cat version

## 编译Android源码模块
> Android源代码目录下的build目录，有envsetup.sh脚本文件，执行该脚本文件，可以获得一些工具  
USER-NAME@MACHINE-NAME:~/Android$ ./build/envsetup.sh


执行完后有如下命令可用  
> - croot: Changes directory to the top of the tree.
- m: Makes from the top of the tree.
- mm: Builds all of the modules in the current directory.
- mmm: Builds all of the modules in the supplied directories.
- cgrep: Greps on all local C/C++ files.
- jgrep: Greps on all local Java files.
- resgrep: Greps on all local res/*.xml files.
- godir: Go to the directory containing a file.


编译  
> 使用mmm命令来编译指定的模块  
USER-NAME@MACHINE-NAME:~/Android$ mmm packages/apps/Email/  

> 使用mm命令来编译当前目录  
USER-NAME@MACHINE-NAME:~/Android/packages/apps/Email/$ mm  


编译后目标放置的目录  
> - apk: out/target/product/generic/system/app  
- C编译的可执行文件: out/target/product/generic/system/bin  
- 动态链接库文件: out/target/product/generic/system/lib  
- 硬件抽象层(HAL)接口文件: out/target/product/generic/system/lib/hw

打包system.img文件命令  
> USER-NAME@MACHINE-NAME:~/Android$ make snod

