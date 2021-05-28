---
title: Django 中 models 相关属性
typora-root-url: ./
date: 2021-05-27 13:50:14
tags: ['Django']
categories: Python
---

## 概念（models）

模型是有关数据的唯一确定的信息源。它包含要存储数据的基本字段和行为。通常，每个模型都映射到单个数据库表。

基础知识：

- 每个模型都是一个子类的Python类 [`django.db.models.Model`](https://docs.djangoproject.com/en/dev/ref/models/instances/#django.db.models.Model)。
- 模型的每个属性代表一个数据库字段。
- 有了这些，Django为您提供了一个自动生成的数据库访问API。请参阅进行[查询](https://docs.djangoproject.com/en/dev/topics/db/queries/)。

## on_delete

在创建一对多、一对一、多对一关系的时候，需要在 `ForeignKey` 的第二参数中加入`on_delete=models.CASCADE` 主外关系键中，级联删除，也就是当删除主表的数据的时候从表中的数据也随着一起删除。

这是数据库外键定义的一个可选项，用来设置当主键表中的被参考列的数据发生变化时，外键表中响应字段的变换规则的。

> **update 是主键表中被参考字段的值更新；delete是指在主键表中删除一条记录**

### on_update 和 on_delete 后面可以跟的属性有四个

1.  no action（python2.x）DO NOTHING（python3.x） 表示 不做任何操作，
2.  set null 表示在外键表中将相应字段设置为null
3.  set default 表示设置为默认值
4.  cascade 表示级联操作，就是说，如果主键表中被参考字段更新，外键表中也更新，主键表中的记录被删除，外键表中改行也相应删除

## 操作数据库（增删改查）

### 1、创建 model 表

```python
from django.db import models
import uuid
class User_Info(models.Model):
    id = models.CharField(max_length=20, primary_key=True)
    name = models.CharField(max_length=100)
    account = models.CharField(max_length=100)
    password = models.CharField(max_length=100)
    phone = models.CharField(max_length=50,null=True)
    role = models.ForeignKey(Roles, on_delete=models.DO_NOTHING, null=True)
```

### 2、生成相应的表

```python
# 执行表创建更改
python manage.py makemigrations

# 数据库映射
python manage.py migrate
```

### 3、增删改查

1. 查

   ```python
   # 查所有
   models.UserInfo.objects.all()
   # 只取name列
   models.UserInfo.objects.all().values('name') 
   # 取出id和user列，并生成一个列表
   models.UserInfo.objects.all().values_list('id','name') 
   # 根据条件 id 查询 
   models.UserInfo.objects.get(id=1)
   # 根据条件 name 查询 
   models.UserInfo.objects.get(name='小明')
   ```

2. 增

   ```python
   # 方案一
   models.UserInfo.objects.create(name='小明',password='123456')
   
   # 方案二
   obj = models.UserInfo(name='小明',pwd='123456')
   obj.save()
   
   # 方案三
   dic = {'user':'yangmv','pwd':'123456'}
   models.UserInfo.objects.create(**dic) # ** 适用于 dict 用于展开
   ```

3. 删

   ```python
   models.UserInfo.objects.filter(id='12345656').delete()
   ```

4. 改

   ```python
   # 方案一
   # 类似 sql 语句删除
   models.UserInfo.objects.filter(id='12345656').update(name='小红')
   > 如果有修改时间 DateTimeField(auto_now=True) 则不会生效，方案二没有问题
   
   # 方案二
   # 获取该对象后对其重新赋值
   obj = models.UserInfo.objects.get(id='12345656')
   obj.name = '小红'
   obj.save()
   ```


## 根据现有数据库自动生成 models 模型文件 - inspectdb

### Django 引入外部数据库：

1. 创建一个项目，修改seting文件，在setting里面设置你要连接的数据库类型和连接名称，地址之类，和创建新项目的时候一致

2. 运行下面代码可以自动生成models模型文件

   ```Python
   Python manage.py inspectdb
   ```

   这样就可以在控制台的命令行中看到数据库的模型文件了

3. 导入数据库内所有的表生成模型文件

   ```python
   python manage.py inspectdb --database=xxx> app/models.py # xxx指的是所选数据库名称
   ```

4. 导入单个数据库表

   ```python
   python manage.py inspectdb table_name > app/models.py
   ```

   例：从数据库中生成的表模型文件

   ```python
   from django.db import models
   
   class User(models.Model):
       id = models.CharField(primary_key=True, max_length=100)
       name = models.CharField(max_length=50, blank=True, null=True)
   
       class Meta:
           managed = False # 不能修改与删除
           db_table = 'user'
   ```

   > managed：表示该表是否可以操作，如需操作设置为 True

## 重置 migrations 下的文件

### 1、不考虑数据表的情况

1. 首先删除数据库中的相关APP下的数据表

2. 然后删除APP下的migration模块中的所有 文件，除了init.py 文件

3. 执行下面的命令

   ```python
   python manage.py makemigrations
   python manage.py migrate
   ```

### 2、保留原有数据表的情况

这个情况是开发中最为常见的，也是操作起来稍微复杂一点的情况，但是只要遵循下面的操作步骤，就不会引发任何错误：

1. 检查migrations文件夹下面的修改记录文件是否与数据库保持一致，如若我们执行这个命令

   ```python
   python manage.py makegrations
   ```

   执行结果显示:

   ```python
   No changes detected
   ```

   那我们就可以继续执行下面的步骤

2. 查看当前项目下所有APP对应的已经生效的（已经成功执行的）migration文件，命令如下：

   ```python
   python manage.py showmigrations
   ```

   结果如下图所示，前面的[x]表示已经执行过的文件，如：imooc这个APP下执行过两次操作

   ```python
   admin
    [X] 0001_initial
    [X] 0002_logentry_remove_auto_add
   auth
    [X] 0001_initial
    [X] 0002_alter_permission_name_max_length
    [X] 0003_alter_user_email_max_length
    [X] 0004_alter_user_username_opts
    [X] 0005_alter_user_last_login_null
    [X] 0006_require_contenttypes_0002
    [X] 0007_alter_validators_add_error_messages
    [X] 0008_alter_user_username_max_length
   contenttypes
    [X] 0001_initial
    [X] 0002_remove_content_type_name
   imooc
    [X] 0001_initial
    [X] 0002_auto_20190122_0929
   sessions
    [X] 0001_initial
   ```

3. 重置你的APP的操作，使它们恢复到没有执行的状态，这里注意一下 `fake` 前面的符号，是两个“-”，另外，`imooc`  是APP的名字，记得将它替换成你的APP名字。还有一点就是不要直接复制，要手敲到终端才不会因格式问题而产生错误。`fake` 参数该怎样理解呢？`fake`  是假装的意思，在这里我们可以理解为假设我们将这些操作设置为没有执行的状态，而不是他们真的没有执行！

   ```python
   python manage.py migrate --fake imooc zero
   ```

   结果如下，则显示重置成功

   ```python
   (wprkplace) D:\PythonProject\learn>python manage.py migrate --fake imooc zero
   Operations to perform:
     Unapply all migrations: imooc
   Running migrations:
     Rendering model states... DONE
     Unapplying imooc.0005_hua... FAKED
     Unapplying imooc.0004_xiao... FAKED
     Unapplying imooc.0003_ming... FAKED
     Unapplying imooc.0002_auto_20190122_0929... FAKED
     Unapplying imooc.0001_initial... FAKED
   ```

   然后我们可以再次执行showmigrations的命令，查看一下各个操作的状态

   可以看到imooc这个APP下面的操作全部重置为没有执行的状态，[x]变成了[ ]，注意是假设它没有执行！

4. 然后放心大胆地删除migrations文件夹下面，**除了__init__.py文件**，的所有的带有序号的py文件，包括pycache文件夹！

5. 执行下面的命令，再次为这个APP 生成 `0001_initial.py` 之类的文件

如果你再去看一下 `django_migrations` 这个表，你会发现这个表添加了一条记录，一般来说如果我们真的执行了数据库的修改操作才会生成这样一条记录，但是我们用了`fake`  这个“假装”的操作，所以其实仅仅是添加了一条记录而已，并没有实际修改数据表，也就是这样，完成了 `migrations` 文件夹的重置！

