---
title: ubuntu下安装命令的区别
date: 2017-05-17 9:10:11
tags: [linux]
categories: 操作系统
comments: true
---
# ubuntu下安装命令的区别（dpkg、apt-get、aptitude）

dpkg绕过apt包管理数据库对软件包进行操作，所以你用dpkg安装过的软件包用apt可以再安装一遍，系统不知道之前安装过了，将会覆盖之前dpkg的安装。

dpkg是用来安装.deb文件,但不会解决模块的依赖关系,且不会关心ubuntu的软件仓库内的软件,可以用于安装本地的deb文件

apt会解决和安装模块的依赖问题,并会咨询软件仓库, 但不会安装本地的deb文件, apt是建立在dpkg之上的软件管理工具

aptitude与 apt-get 一样，是 Debian 及其衍生系统***能极其强大的包管理工具。与 apt-get 不同的是，aptitude在处理依赖问题上更佳一些。举例来说，aptitude在删除一个包时，会同时删除本身所依赖的包。这样，系统中不会残留无用的包，整个系统更为干净。

## 安装软件包
```
dpkg          -i                package_name.deb #安装本地软件包，不解决依赖关系
apt-get     install      package #在线安装软件包
aptitude   install      pattern #同上

apt-get       install    package   –reinstall   #重新安装软件包
apitude     reinstall    package      #同上

```
## 移除软件包
```
dpkg          -r         package #删除软件包
apt-get      remove       package #同上
aptitude     remove    package #同上

dpkg         -P             #删除软件包及配置文件
apt-get     remove       package –purge      #删除软件包及配置文件
apitude     purge         pattern #同上
```
## 自动移除软件包
```
apt-get autoremove #删除不再需要的软件包
注：aptitude 没有，它会自动解决这件事

```
## 清除下载的软件包
```
apt-get         clean #清除 /var/cache/apt/archives 目录
aptitude       clean #同上

apt-get       autoclean #清除 /var/cache/apt/archives 目录，不过只清理过时的包
aptitude        autoclean #同上

```
## 编译相关
```
apt-get source package #获取源码

apt-get          build-dep   package #解决编译源码 package 的依赖关系
aptitude        build-dep    pattern #解决编译源码 pattern 的依赖关系

```
## 平台相关
```
apt-cross –arch ARCH –show package 显示属于 ARCH 构架的 package 软件包信息
apt-cross –arch ARCH –get package #下载属于 ARCH 构架的 package 软件包
apt-cross –arch ARCH –install package #安装属于 ARCH 构架的 package 软件包
apt-cross –arch ARCH –remove package #移除属于 ARCH 构架的 package 软件包
apt-cross –arch ARCH –purge package #移除属于 ARCH 构架的 package 软件包
apt-cross –arch ARCH –update #升级属于 ARCH 构架的 package 软件包

```
***注：慎重考虑要不要用这种方法来安装不同构架的软件包，这样会破坏系统。对于 amd64 的用户可能需要强制安装某些 i386 的包，千万不要把原来 amd64 本身的文件给 replace 了。最好只是安装一些 lib 到 /usr/lib32 目录下。同样地，可以用 apt-file 看某个其它构架的软件包包含哪些文件，或者是文件属于哪个包，不过记得最先要用 apt-file –architecture ARCH update 来升级 apt-file 的数据库，在 search 或 show 时也要指定 ARCH。***

## 更新源
```
apt-get       update #更新源
aptitude     update #同上

```
## 更新系统
```
apt-get             upgrade #更新已经安装的软件包
aptitude           safe-upgrade #同上
apt-get            dist-upgrade #升级系统
aptitude          full-upgrade #同
```

