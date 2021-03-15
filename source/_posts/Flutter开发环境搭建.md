---
title: Flutter开发环境搭建
date: 2021-01-28 11:37:23
tags: [Flutter,环境搭建]
categories: 移动端框架
typora-root-url: ./
---
## Flutter 开发环境搭建

系统的基本要求

JAVA环境的安装

Flutter SDK 的安装

## 系统基本要求

操作系统：windows7以上64位操作系统

磁盘空间：大于3G,最好可以达到5G

## Java 环境的安装

Java 环境下载https://www.oracle.com/java/technologies/javase-jdk8-downloads.html

安装完成后在终端里使用 java命令检查

首先下载，同意协议，下载windows64

安装的时候一直点击下一步

安装完成后打开命令行输入 java 有显示就说明安装成功了

## Flutter SDK 的安装

下载地址：https://flutter.io/sdk-archive/#windows

配置环境变量

fultter doctor 命令使用

首先下载最新的flutter ，

把压缩包解压到我们想放的文件夹下面，

第二步,复制bin路径配置环境变量

最后，打开命令行输入 flutter 检测是否安装好了

## 电脑上配置Flutter国内镜像

地址：https://flutter-io.cn/     中文社区资源

国内：https://flutter.dev/community/china

### fultter doctor 命令使用

检测是否安装，绿勾表示安装好了，红×表示未安装，后面两项警告，安卓studio未安装以及虚拟机未挂载。

### Android Studio 的安装

下载地址： https://www.androiddevtools.cn/ 

在安装过程中需要下载一系列插件，如果之前未安装过的话。建议安装3.3.0正式版，比较稳定。

随后将sdk下的platform-tools与tools添加到环境变量中。

##### Dart 和 Flutter 插件的安装

打开Android Studio ,找到 Plugin 进行安装；

出现 Search in repositories 时 点击进入；

安装 Flutter 会自动安装 Dart 插件

**注意：如果出现插件库无法显示，那就在系统设置里将 Updates--> Use secure  connection(安全连接)勾去掉，最后重启电脑即可**

最后，在命令行执行检测 flutter doctor

接受协议， flutter doctor --android-licenses  执行下

一直，y 下去

出现这样就ok 了，最后再执行 flutter doctor 检测下

可以看到就剩一个虚拟机未安装了！

###  AVD 虚拟机的建立





## 新建一个Flutter 项目

**步骤一**：打开Android Studio, 选择新建 Flutter project

再次选择Flutter Application,需要等待一会

尽量配置公司一个域名；

这个包名是com.公司域名.项目名

finish 等待几分钟

**步骤二：**最后生成项目的时候需要从新导入

  点击android,在当前窗口打开

##### 注意：第一次打开会有种卡死状态，其实是在下载Grade Files 文件，大概十几分钟看网速！

**易出错！！！**

可以看这个目录下，文件大小是否在变，在变就说明正常，如果没变有可能安装失败！

如果出错：解决方法见下图

如果多次下载失败，可以将电脑连接手机热点下载尝试！！！

**步骤三：**下载后之后就可以run 运行了

这里具需要连接**Android** 手机，并且**手机打开热点**，然后打开**调试模式**

### Vscode 配置

安装dart  与  flutter

打开文件夹，

flutter run 启动应用

在命令面板中   flutter device  查看支持的设备

