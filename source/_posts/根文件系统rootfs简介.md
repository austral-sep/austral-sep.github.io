---
title: 根文件系统rootfs简介
date: 2021-12-31 15:30:00
tags: rootfs 
categories: 
- Linux
	- 嵌入式
---

# Root File System


## 文件系统简介

Linux支持多种文件系统，包括ext2、ext3、vfat、ntfs、iso9660、jffs、yaffs、romfs和nfs等，为了对各类文件系统进行统一管理，Linux引入了虚拟文件系统VFS(Virtual File System)，为各类文件系统提供一个统一的操作界面和应用编程接口。

## 根文件系统目录内容简介
- bin :基本的可执行文件 
- opt :添加的软件包
- boot :启动时需要的一些文件
- proc :内核及进程信息的虚拟文件系统
- dev : 设备文件
- root:root用户目录
- etc: 系统配置文件
- sbin:系统管理的程序
- home : 用户目录
- tmp : 临时文件
- lib : 库文件
- usr : 里面存放的是共享、只读的程序和数据，这表明/usr目录下的内容可以在多个主机间共享，这些主要也符合FHS标准的。
- mnt : 挂载文件系统的挂载点
- var : 存放系统日志或一些服务程序的临时文件

## 嵌入式环境需要移植的目录    

`/bin /sbin /etc /proc /tmp /var /dev /mnt`

1. Linux的根文件系统至少应包括以下几项内容。基本的文件系统结构，包含一些必需的目录比如：/dev，/proc，/bin，/etc，/lib，/usr，/tmp等。
2. 基本程序运行所需的库函数，如glibc。
3. 基本的系统配置文件，比如rc.sysinit，inittab等脚本文件。
4. 必要的设备文件支持：/dev/hd*，/dev/tty*，/dev/fd0。
5. 基本的应用程序，如sh，ls，cp，mv等。

## 移植需要做的工作

1. 把全局配置文件放入/etc目录下。
2. 将设备文件信息放入/dev目录下，设备名可以作为符号链接定位在/dev中或/dev子目录中的其他设备存在。
3. 操作系统核心定位在/或/boot，若操作系统核心不是作为文件系统的一个文件存在，不应用它。
4. 库存放的目录是/lib。
5. 存放系统编译后的可执行文件、命令的目录是/bin，/sbin，/usr。

