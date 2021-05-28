---
title: Django-基础
typora-root-url: ./
date: 2021-05-28 09:21:31
tags: ['Django']
categories: python
---

## 简介

Django 是一个由 Python 编写的一个开放源代码的 Web 应用框架。

使用 Django，只要很少的代码，Python 的程序开发人员就可以轻松地完成一个正式网站所需要的大部分内容，并进一步开发出全功能的 Web 服务 Django 本身基于 MVC 模型，即 Model（模型）+ View（视图）+ Controller（控制器）设计模式，MVC 模式使后续对程序的修改和扩展简化，并且使程序某一部分的重复利用成为可能。

> 以上摘于菜鸟教程

## 项目搭建

1. 建项

   ```python
   django-admin startproject 项目名
   ```

2. 创建模块

   ```python
   python manage.py startapp 功能模块名
   ```

3. 启动项目

   ```python
   python manage.py runserver 或者指定端口号也可以
   ```

## 项目目录简述

- **migrations**：用于记录models中数据的变更
- **admin.py**：映射models中的数据到Django自带的admin后台
- **apps.py**：用于应用程序的配置，在新的Django版本中新增文件
- **models.py**：Djanngo的模型文件，创建程序数据表模型（对应数据库的相关操作）
- **tests.py**：创建Django测试用例
- **views.py**：Django的视图文件，控制向前端页面显示的内容即我们常说的接口

在此情况下，CMD内输入：python manage.py runserver，打开浏览器。输入地址：127.0.0.1:8000，则会显示：It worked

如果8000端口被占用，则在启动时可以指定ip地址以及端口，python manager.py runserver 127.0.0.1:8001，在浏览器内输入对应的ip以及端口即可

## 依赖导入导出

平时导出项目依赖都是 `pip freeze > requirements.txt`，这种导出的是当前 python 环境中所有的包，只会多不会少，有些库不是必须的也跟着导出来， 冗余过重。这个时候 `pipreqs` 这个库就派上用场了，它只会导出当前项目运行的虚拟环境所依赖的包，环境中多余的库就不会导出，在迁移环境时候很方便。

```python
# 可以在全局环境中安装该库
pip install pippreqs

# 在当前项目根目录生产生成一个requirements.txt 文件
pipreqs ./

# 如下所示
PyMySQL==0.10.1
Django==3.1.2
Flask==1.1.2
pycryptodome==3.9.8
PyJWT==1.7.1
Werkzeug==1.0.1

# 对新建的虚拟环境进行安装这些依赖包
pip install -r requirements.txt
```

> 注意：如果报gbk编码错误，更改pipreqs.py文件啊，将encoding改为utf-8即可

## 时间与时区设置问题
情景：Django 里边使用datetime模块，发现时间总是延迟了8小时

**解决方法如下：**
+ 在 Django 的配置文件 `settings.py` 中，有两个配置参数是跟时间与时区有关的，分别是 `TIME_ZONE` 和 `USE_TZ`
+ 如果 `USE_TZ` 设置为 True 时，Django 会使用系统默认设置的时区，即 `America/Chicago`，此时的 `TIME_ZONE` 不管有没有设置都不起作用。
+ 如果 `USE_TZ` 设置为 False，而 `TIME_ZONE` 设置为 None，则Django 还是会使用默认的 `America/Chicago` 时间。若 `TIME_ZONE` 设置为其它时区的话，则还要分情况，如果是 Windows 系统，则 `TIME_ZONE`设置是没用的，Django 会使用本机的时间。如果为其他系统，则使用该时区的时间，入设置 `USE_TZ = False` , `TIME_ZONE = 'Asia/Shanghai'`, 则使用上海的 UTC 时间。
