---
title: Dart介绍及环境搭建
typora-root-url: ./
date: 2021-01-28 15:47:08
tags: [Dart,Dart环境搭建]
categories: 编程语言
---
## Dart 概述

- Dart 是Goole 发布的一门开源编程语言

- Dart初期目标是成为下一代的web开发语言，也就是替代 javascript

- Dart 是全平台开发

- Dart 是一门**面向对象**的编程语言


## 应用场景

web 开发

跨平台移动应用开发（flutter）

脚本或服务端开发

版本

Dart 1.x

Dart 2 目前是开发版本

## 开发环境搭建

### SDK安装

（1）Windows平台：choco install dart-sdk

（2）Linux平台：sudo apt-get install dart

（3）Mac平台：brew install dart--devel

参考官网：https://www.dartcn.com/tools/sdk/#install

这里我使用Windows系统安装

### 方法一：

```
使用Chocolatey安装
要使用Chocolatey安装Dart SDK 的稳定版本，请运行以下命令：
C:\> choco install dart-sdk

要安装开发版本，请运行以下命令：
C:\> choco install dart-sdk --pre

要升级 Dart SDK，请运行以下命令（添加--pre以升级开发版本）:
C:\> choco upgrade dart-sdk

由于使用chocolatey,所以我们要先安装这个chocolatey
介绍：程序包管理器，现代软件自动化安装，并添加系统环境变量。

install with cmd.exe

@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "[System.Net.ServicePointManager]::SecurityProtocol = 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

### 方法二：直接将代码jdk下载下来随后添加系统环境变量即可。

最后检测下，是否安装成功

网上代码托管地址：https://gitee.com/LeiYu-Io/Dart-Basic

然后我们需要个IDE,例如vscode等，然后再下载dart插件即可