---
title: MySQL-8.0.23安装配置
typora-root-url: ./
date: 2021-02-10 15:01:13
tags:
categories:
---

## 网盘链接

链接：https://pan.baidu.com/s/1MCp43CklgTQz_qCXMAqluQ

提取码：26wt

## 配置

- 下载完后，我们将 zip 包解压到相应的目录，这里我将解压后的文件夹放在**C:\webapp\mysql-8.0.23-winx64**下。

- **接下来我们需要配置下 MySQL 的配置文件**

打开刚刚解压的文件夹 **C:\web\mysql-8.0.11** ，在该文件夹下创建 **my.ini** 配置文件，编辑 **my.ini** 配置以下基本信息：

```
[client]
# 设置mysql客户端默认字符集
default-character-set=utf8
 
[mysqld]
# 设置3306端口
port = 3306
# 设置mysql的安装目录
basedir= C:\webapp\mysql-8.0.23-winx64
# 设置 mysql数据库的数据的存放目录，MySQL 8+ 不需要以下配置，系统自己生成即可，否则有可能报错
# datadir=C:\\web\\sqldata
# 允许最大连接数
max_connections=20
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

![image-20210211165815651](image-20210211165815651.png)

- **初始化**

  运行cmd（管理员方式）然后cd到mysql安装的目录的bin文件夹下，切记！管理员方式

  ![image-20210211163635763](image-20210211163635763.png)

然后执行以下命令

```
mysqld --initialize --console
```

![image-20210211163804662](image-20210211163804662.png)

一定要记住生成的临时密码，后期我们再改！

- **安装启动mysql服务**

  ```
  mysqld --install mysql
  ```

  ![image-20210211164115669](image-20210211164115669.png)

  安装成功后，然后启动

  ```
  net start mysql
  ```

  ![image-20210211164241652](image-20210211164241652.png)

  

- **更改密码**

  进行登录数据库，这时提示需要密码，然后就是用你上面的初始化时候密码登录

  ```
  mysql -u root -p
  ```

  修改密码语句：

  ```
  ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
  ```


## Navicat15安装及破解

链接: https://www.cnblogs.com/Kathrine/p/12844846.html

