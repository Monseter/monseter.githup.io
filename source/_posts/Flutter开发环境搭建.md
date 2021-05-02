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

AVD 是通过 AS 建立的，方便在手机上调试而不用去下载其他模拟器或者使用真机连接调试。

点击 AS 菜单栏 AVD 进行选择，如果没有那么根据自己所需进行选择，Android 版本不同以及它的操作系统。操作系统需要安装，根据提示直接 Install  就可以了。

AVD 虚拟机创建后我们就可以直接点击启动看看效果。大多数情况下我们使用 VS code 开发，那么开虚拟机只能通过开 AS 才能开启 AVD，我们可以建个配置文件一键启动我们的 AVD。

需要知道，我们的 AVD 路径一般在 用户盘  `.android\avd`

.bat 文件内容如下：

```
...\AppData\Local\Android\Sdk\emulator\emulator.exe -netdelay none -netspeed full -avd Pixel_2_API_30
```

`...\AppData\Local\Android\Sdk\emulator\emulator.exe` 为 emulator.exe 路径

`-netdelay none -netspeed full ` 启动配置项

`-avd Pixel_2_API_30`  对应的是我们 AVD 名称

最后，双击 bat 文件就能看到 cmd  启动窗口，以及 AVD 手机模拟器。

## 新建一个Flutter 项目

**步骤一**：打开Android Studio, 选择新建 Flutter project

再次选择Flutter Application,需要等待一会

尽量配置公司一个域名；

这个包名是com.公司域名.项目名

finish 等待几分钟

## gradle 安装

**方法一离线安装：**该方法需要手动下载对应的 `Android Studio` 对应的 `gradle` 版本号

gradle 下载地址: https://services.gradle.org/distributions/

1. 上述地址中下载需要的Gradle文件，保存在规范路径（不解压 ）。
2. 打开Android studio工程；
3. 切换到 Android 视图；
4. 找到 Gradle Scripts ；
5. 找到gradle-wrapper.properties ；（gradle-wrapper.properties文件是专门管理gradle 的配置文件）
6. 将distributionUrl 改为第1步中Gradle文件的路径；
7. 同步或重新打开工程，离线配置gradle完成。

示例如下：

*distributionUrl=file:///D:/Android/gradle/gradle-3.3-all.zip*

```
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=file:///D:/Android/gradle/gradle-3.3-all.zip
```

> 建议使用此离线下载方法，在线下载比较慢而且容易出问题。

**方法二在线安装：**更改 android 下 build.gradle 配置镜像 不然下载特慢

```
// Top-level build file where you can add configuration options common to all sub-projects/modules.
buildscript {
    ext {
        kotlin_version = '1.3.72'
    }
    repositories {
        maven{url 'http://maven.aliyun.com/nexus/content/groups/public/'}
        maven{url "https://jitpack.io"}
        google()
//        jcenter()
    }
    dependencies {
        classpath "com.android.tools.build:gradle:4.0.1"
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        maven{url 'http://maven.aliyun.com/nexus/content/groups/public/'}
        google()
//        jcenter()
    }
}
```

然后，重新将项目根目录中的 android  导入 Android Studio 即可。最后，点击 `File > Sync Project with Gradle Files `

  点击android,在当前窗口打开

> 注意：第一次打开会有种卡死状态，其实是在下载Grade Files 文件，大概十几分钟看网速！

**易出错！！！**

可以看 系统用户 .gradle/wraper/dist 目录下，文件大小是否在变，在变就说明正常，如果没变有可能安装失败！

如果出错：解决方法见下图

如果多次下载失败，可以将电脑连接手机热点下载尝试！！！

**步骤三：**下载后之后就可以run 运行了

这里具需要连接**Android** 手机，并且**手机打开热点**，然后打开**调试模式**

### Vscode 配置

安装dart  与  flutter

打开文件夹，

flutter run 启动应用

在命令面板中   flutter device  查看支持的设备

