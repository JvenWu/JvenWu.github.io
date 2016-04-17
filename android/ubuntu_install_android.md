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

在/etc/profile中添加：

export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_65

export JRE_HOME=${JAVA_HOME}/jre

export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib

export PATH=${JAVA_HOME}/bin:$PATH

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

##下载安桌工程
wget https://dl-ssl.google.com/dl/googlesource/git-repo/repo

chmod 777 repo

cp repo /bin/

mkdir Android

cd Android

repo init -u https://dl-ssl.google.com/dl/googlesource/git-repo/repo

repo sync

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
