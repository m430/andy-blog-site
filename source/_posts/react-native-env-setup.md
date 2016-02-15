title: ReactNative开发环境搭建攻略
date: 2016-02-15 16:14:25
categories: ReactNative
tags: [ReactNative, React]
---

### 什么是react-native?
`react-native`是facebook公司基于`react`框架研发的面向移动端的开发框架，你可以用它来开发类似原生应用的终端程序，它最大的优点就是跨平台，终极目标是一套代码可以共用在android和ios上，为什么说终极目标呢？因为这个想法非常牛逼，大大降低了开发成本和时间，但是现在它还不完全能达到这个效果，作为一个新生儿它还在不断的长大，不过它为我们开发移动应用开辟了一条希望的大道，目前已经有很多公司开始在生产环境中实践RN。这阵子不太忙研究了一下`react-native`，觉得挺好玩的，把学习历程做个记录，中间也遇到了一些坑我会特别说明。

<!-- more -->

### 环境配置
首先因为我是windows环境，只能搞android版本的了，所以先要[安装好android的开发环境](http://reactnative.cn/docs/android-setup.html#content)，这里就不赘述了，请点开链接自行安装。
然后下一步有一些必要的软件需要安装：
- nodejs 最好最新版
- python 2.7.x版本
- git 

真机调试的话需要用手机打开`USB调试`，然后用USB连接电脑，我的手机是MX4，推荐直接用豌豆荚，确保手机和豌豆荚连接成功后可以进行下一步了(这一步可以用adb测试)。下一步开始RN相关的。先要安装`react-native-cli`模块：
```bash
npm install -g react-native-cli 
```
然后初始化一个`react-native`项目：
```bash
react-native init reactNativeApp
```
然后进入`reactNativeApp`目录启动packager：
```bash
react-native start
```
启动后你会看到如下图：

![](img/rn1.jpg)

说明已经正常启动了，然后可以访问这个链接[http://localhost:8081/index.android.bundle?platform=android](http://localhost:8081/index.android.bundle?platform=android)来看一下有没有报错，如果没有，就可以下一步开始安装apk了。从新开启一个命令行到目录reactNativeApp下，然后执行：
```bash
react-native run-android
```
你会发现到最后会报错如下图：

![](img/rn2.jpg)

刚开始被卡在这里了，我很郁闷，后来换成华为的手机发现可以正常安装，然后我想应该是因为系统权限的问题，所以干脆手动安装好了，通过豌豆荚直接安装，如下图：
![](img/rn3.jpg)

安装成功后，手机上就可以点开了应用了，点开后你会发现是白屏，这是因为悬浮窗的权限没有打开的问题，在`设置-> 应用管理-> 找到reactNativeApp-> 权限管理-> 打开悬浮窗`。重启app，这时你会看到一个红屏如下：
![](img/rn4.jpg)

这是因为没有设置服务器地址和端口，用手机摇一摇然后会看到下面这张图：
![](img/rn5.jpg)

点击`Dev Settings`然后进去后点击`Debug Server host & port for device`，然后看到下图
![](img/rn7.jpg)

输入电脑的IP和端口（默认8081）,确定后点击返回按钮，再摇一摇手机，出来的菜单中点击`reload js`。很开心的看到下面的画面
![](img/rn8.jpg)

至此，在windows下的RN开发环境就搭建好了，前端er, 你可以开发自己的app咯。炫酷吊炸天有木有。