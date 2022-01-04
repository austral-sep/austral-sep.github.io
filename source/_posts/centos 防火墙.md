---
title: Centos防火墙
date: 2021-12-29 16:38:10
tags: firewalld
categories: 网络
---
## 安装firewalld
```bash
$ yum install firewalld -y
```

## 防火墙命令

```	bash
$ systemctl start firewalld # 开启防火墙
$ systemctl enable firewalld # 开启开机自启
$ systemctl status firewalld
$ systemctl disable firewalld
$ systemctl stop firewalld
```

## 配置firewalld-cmd

```bash
$ firewall-cmd --state # 显示状态
$ firewall-cmd --zone=public --add-port=80/tcp --permanent # 开放80端口，--permanent表示重启有效
$ firewall-cmd --zone=public --remove-port=80/tcp --permanent # 取消开放80端口
$ firewall-cmd --reload # 更新防火墙规则
$ firewall-cmd --zone=public --list-ports # 查看所有打开的端口
```
	 
