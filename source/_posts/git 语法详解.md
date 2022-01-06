---
title: git语法详解
date: 2022-01-05 17:32
categories:
- git
---
![](https://www.runoob.com/wp-content/uploads/2015/02/git-command.jpg)

workspace: 工作区
staging area: 暂存区
local repository: 本地版本库
remote repository: 远程版本库


## git配置
设置 /etc/gitconfig
当前项目（git 仓库）用户信息设置
```shell
$ git config user.name "usr"
$ git config user.email "email"
```
用户信息全局设置
```shell
$ git config --global user.name "usr"
$ git config --global user.email "email"
```
git使用的默认编辑器
```shell
$ git config --global core.editor vim
```
差异分析工具
```shell
$ git config --global merge.tool vimdiff
```
查看配置信息
```shell
$ git config --list
```
本地分支关联远程分支
```shell
$ git branch --set-upstream-to=origin/\<branch> master
```

## git操作

### 创建仓库命令
将一个目录初始化为git仓库
```shell
$ git init
```
初始化一个不包含工作区的，只保存git历史提交版本信息的git仓库，即为裸仓库。一般用作为远程备份或公共版本库。
```shell
$ git init --bare
```


### 克隆和拉取远程仓库
在本地克隆/下载一个完整的远程仓库，包括全部的版本库记录。
```shell
$ git clone <版本库的URL> [本地目录名]
$ git remote -v # 打印远程仓库信息
```
将远程仓库拉取到本地版本库，本地目录需先初始化并添加远程仓库。
```shell
$ git init # 初始化
$ git remote add origin git@github.com:name/repo.git # 添加远程仓库
$ git branch --set-upstream-to=origin/\<branch> master # 关联远程分支
$ git fetch
```
将远程仓库拉取到工作区，同样需先初始化并添加远程仓库。
```shell
# 同上
$ git pull
```
or
```shell
$ git fetch
$ git merge
```



### 提交与修改
查看仓库当前的状态，显示有变更的文件
```shell
$ git status
```

将工作区的文件记录到到暂存区中
```shell
$ git add . # 将所有新文件/修改过的文件添加到暂存区
$ git add <file> # 将单独文件添加到暂存区
```

将记录从暂存区提交到本地版本库
```shell
$ git commit -m "要记录的信息"
$ git commit -am "要记录的信息" #这条命令可以将所有修改过的文件一步从工作区提交到版本库
```

将变化推送到远程版本库
```shell
$ git push # 表示将本地当前分支推送到默认主机的同名分支上
```

### 分支管理
列出分支
```shell
$ git branch
```

创建分支
```shell
$ git branch (branchname)
```

切换分支
```shell
$ git checkout (branchname)
```

合并分支
```shell
$ git merge
```

删除分支
```shell
$ git branch -d (branchname)
```

删除远程版本库的master分支
```shell
$ git push origin --delete master
```

### 回退
```shell
$ git reset HEAD
```
暂存区的目录树会被重写，被 master 分支指向的目录树所替换，但是工作区不受影响。
```shell
$ git rm --cached <file>
```
直接从暂存区删除文件，工作区则不做出改变。
```shell
$ git checkout .
```
用暂存区全部或指定的文件替换工作区的文件。这个操作很危险，会清除工作区中未添加到暂存区中的改动。
```shell
$ git checkout HEAD .
```
用 HEAD 指向的 master 分支中的全部或者部分文件替换暂存区和以及工作区中的文件。这个命令也是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动。

## git标签
给最新一次提交打上（HEAD）"v1.0"的标签
```shell
git tag -a v1.0 
```


## git日志
查看日志
```shell
$ git log
```


以列表形式查看<font style = "background: rgb(200,100,100)">指定文件</font>的历史修改记录
```shell
$ git blame <file> 
```







