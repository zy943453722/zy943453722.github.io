---
title: git与github基本操作
date: 2017-05-10 20:03:31
tags: [git,github]
categories: 研发管理
comments: true 
---
# git与github的基本操作
## 一、准备：
1、git config --global user.name "Firstname Lastname"

2、git config --global user.email"your_email@example.com"

3、ssh-keygen -t rsa -C "your_email@example.com"添加ssh，一路回车略过

4、id_rsa.pub寻找公开公钥复制到github

5、ssh -T git@github.com 测试ssh是否生效

## 二、向远程仓库提交本地文件：
**方法一：**

1、git clone git@github.com:username/example.git

2、cd example

3、git add filename//将文件发到缓存区

4、git diff HEAD//查看本次提交与之前提交的区别（可省略）

5、git commit -m "contribtion"//提交文件

6、git status//查看当前工作树情况（可省略）

7、git log -p//查看提交日志，并可查看修改的情况（可省
略）

8、git push//直接提交到远程仓库

**方法二：**

1.git init

2.git add filename//将文件发到缓存区

3.git diff HEAD//查看本次提交与之前提交的区别（可省略）

4.git commit -m "contribtion"//提交文件

5.git status//查看当前工作树情况（可省略）

6.git log -p//查看提交日志，并可查看修改的情况（可省略）

7.git remote add origin

8.git@github.com:usrname/example.git//创建远程仓库


9.git push -u origin master//把本地当前分支上的内容推送到远程origin库的master分支

**-u参数可以在推送的同时,将 origin 仓库的 master 分
支设置为本地仓库当前分支的 upstream(上游)。添加了这个参数,将来
运行 git pull命令从远程仓库获取内容时,本地仓库的这个分支就可
以直接从 origin 的 master 分支获取内容,省去了另外添加参数的麻烦。**

**工作树（当前文件夹） ----> 暂存区 ----->本地仓库 ----->远程仓库**