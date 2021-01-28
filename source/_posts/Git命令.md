---
title: Git命令
typora-root-url: ./
date: 2021-01-28 14:39:46
tags: Git
categories: 开发工具
---
## 拉取文件步骤
    # 首次需要 clone 仓库地址
    git clone +地址名 分支名
    
    # 第一次clone后都换成pull命令
    git pull origin master //orgin后面跟的是分支
    
    # 我们也可以定义 origin 变量
    git remote add origin 仓库地址
    
    # 如果需要拉指定的分支
    git clone -b 分支名  https://仓库链接
**注意：**

不管我们是推送还是拉取文件，都需要执行下`git status `命令检查下，本地仓库文件是否有更改，避免冲突。

## 文件推送

```
# 切记每次推送前先拉下，多人开发过程中会容易出现冲突
git status 肯定很多红的 是新改动的文件
git add .  把本地新改动的文件放到一个区域里  等待上传
git status 肯定出现很多绿的 说明放到区域里了
git commit -m "备注信息"
git status 肯定出现一段英文 说区域里为空
git push origin 你的分支 切记不能上传到master
```

## 分支创建、修改、删除

```
git branch  name   // 创建本地分支
git branch -a  / / 查看已有的本地及远程分支
git branch -d 分支名    // 删除本地分支，每次合并后就可以删掉
git push origin --d 分支名    //  删除远程分支

git checkout -b 分支名  // 创建并切换本地分支 相当于复制仓库并从新创建分支
git push origin 远程分支名  // 创建远程分支名 默认推送本地仓库名
git push origin dev:fix-1  // 此时创建的远程仓库名为 fix-1
git branch -m OldBranchName NewBranchName   // 修改分支名
```

## 分支合并

```
# 首先切回 master(你的开发分支，基础分支)
git checkout master
# 其次合并当前分支，必须你的文件都已经提交到暂存区
git merge 分支名 
# 最后推送下
git push origin branch
```

## 版本标签

```
git tag v0.1   创建标签名为 v0.1
git show v0.1    查看该标签详细信息

# 标签只会本地创建不会推送到远程
git push origin v0.1   推送标签到远程端
git push origin --tags  一次性推送全部未推送到远程的本地标签

# 如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除
git tag -d v0.1
# 最后
git push origin :refs/tags/v0.1
```

## 版本回退

描述：回退是我们开发过程中经常需要操作的，冲突，版本回退都是需要的。

```
#可以查看所有提交及删除的分支版本信息 比 git log  还要强
git reflog

例： 6141b4a HEAD@{1}: merge base: Merge made by the 'recursive' strategy.
git reset 6141b4a

注意：回退该版本不会清空暂存区，需要从新提交，最好看下暂存区，一般回退是你提交的上一个版本
```



