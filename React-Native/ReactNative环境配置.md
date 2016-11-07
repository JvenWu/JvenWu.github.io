## for React Native: Environment Config | 环境配置

先参考官方文档完成基本配置：https://facebook.github.io/react-native/docs/getting-started.html  

### 需要安装的有：
### 1. Homebrew
Homebrew 是 Mac 中的一个包管理器。可用 brew update 升级。 可用 brew -v查看版本号  

### 2. 安装 watchman 和 flow
这两个包分别是监控文件变化和类型检查的。安装如下：  
```
brew install watchman
brew install flow
```

### 3. App开发环境的设置
3.1 iOS  
XCode 6.3 及其以上即可。  

3.2 Android  
设置环境变量：ANDROID_HOME  

### 4. 运行项目
不管是 iOS 还是 Android，在开发调试阶段，都需要在 Mac 上启动一个 HTTP 服务，  
称为“Debug Server”，默认运行在 8081 端口，APP 通 Debug Server 加载 js。  
iOS 和 Android 的模拟器，连接 Mac 本机的服务都很方便。  
但是通过 USB 或者 WiFi 连接调试，就稍微麻烦一些了。  

4.1 iOS  
还是非常简单，XCode 打开项目，点击运行就好。修改 index.ios.js,  
在模拟器中 ⌘ + R 重新载入 js 即可看到相应的变化。  
iOS 真机调试也简单，修改HTTP地址即可。  
```objective
jsCodeLocation = [NSURL URLWithString:@"http://localhost:8081/index.ios.bundle"];
```

4.2 Android  
按照官方文档，需要一个模拟器（Genymotion模拟器也可以）。但是不像 iOS，  
Android 开发平时更多是直接用真机进行开发和调试。  

运行命令：
```
react-native run-android
```
然后就会部署到模拟器，修改 index.android.js ，调出模拟器菜单键，  
选择重新载入 js 即可看到变化。  

4.3 Android 真机调试
示例 App 直接部署到真机，会有红色界面报错，提示无法连接到 Debug Server。
如果是 5.0 或者以上机型，可通过 adb 反向代理端口，将 Mac 端口反向代理到测试机上。
命令：adb reverse tcp:8081 tcp:8081
如果 5.0 以下机器，应用安装到测试机上之后，摇动设备，
在弹出菜单中选择 Dev Setting > Debug Server host for device，
然后填入 Mac 的 IP 地址+端口号8081（ifconfig 命令可查看本机 IP）
如填入192.168.1.3:8081

4.4 在 Android Studio 中调试开发
我们可能希望在 Android Studio 打开项目，然后编译部署到真机。
这个时候，在命令行启动 Debug Server 即可：
react-native start
