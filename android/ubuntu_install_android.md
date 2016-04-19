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

##下载安桌工程  
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
59.24.3.173cache.pack.google.com  
然后重新输入  
$ repo sync

##编译安装源码：在Android目录下 make

##编译安装源码：在Android目录下 make sdk

##安装编译好的Android镜像到模拟器上
1. 设置环境变量：

USER-NAME@MACHINE-NAME:~/Android$ export PATH=$PATH:~/Android/out/host/linux-x86/bin  

USER-NAME@MACHINE-NAME:~/Android$ export ANDROID_PRODUCT_OUT=~/Android/out/target/product/generic

其中，~/Android/out/host/linux-x86/bin有我们要执行的emulator命令，

而~/Android/out/target/product/generic是Android镜像存放目录，下面执行emulator命令时会用到。

2. 运行模拟器。

USER-NAME@MACHINE-NAME:~/Android$ emulator

模拟器运行需要四个文件，分别是Linux Kernel镜像zImage和Android镜像文件system.img、userdata.img和ramdisk.img
