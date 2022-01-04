---
title: Nginx使用
date: 2021-12-30 12:30:00
tags: web轻量服务器
---

# *Nginx*

### 优点
- 稳定性
- 功能集
- 示例配置文件
- 低系统资源的消耗


### 功能
- 反向代理
- 负载均衡
- 动静分离


## 安装流程

#### 安装pcre依赖
PCRE 作用是让 Nginx 支持 Rewrite 功能。

1. 下载pcre压缩包
```bash
$ wget http://downloads.sourceforge.net/project/pcre/pcre/8.37/pcre-8.37.tar.gz
```
2. 解压缩文件包
```bash
$ tar -xvf  pcre-8.37.tar.gz
```
3. 进入解压后的目录，执行以下命令
```bash
$ .configure
```
4. 使用以下命令进行编译安装
```bash
$ make && make install
```
5. 查看pcre是否安装成功
```bash
$ pcre-config --version
```

#### 安装openssl, zlib等依赖

```bash
$ yum -y install make zlib zlib-devel gcc-c++ libtool openssl openssl-devel
```

#### 安装Ngnix

1. 下载并解压缩包
```bash
$ cd /usr/local/src/ && wget http://nginx.org/download/nginx-1.6.2.tar.gz && tar zxvf nginx-1.6.2.tar.gz
```
2. 检查并编译安装
```
$ .configure --prefix=/usr/local/webserver/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre=/usr/local/src/pcre-8.35 && make && make install
```
3. 查看是否安装成功
```
$ /usr/local/webserver/nginx/sbin/nginx -v
```

## 配置
1. 修改配置文件
```bash 
$ vim /usr/local/webserver/nginx/conf/nginx.conf
```
2. 检查配置正确性命令
```bash
$ /usr/local/webserver/nginx/sbin/nginx -t
```

## Nginx 常用命令
```bash
$ /usr/local/webserver/nginx/sbin/nginx # 启动 Nginx
$ /usr/local/webserver/nginx/sbin/nginx -s reload # 重新载入配置文件
$ /usr/local/webserver/nginx/sbin/nginx -s reopen # 重启 Nginx
$ /usr/local/webserver/nginx/sbin/nginx -s stop # 停止 Nginx
```

#### 基础配置文件
分为：全局快、events块、http全局块

```config
#user  nobody;
worker_processes  1; #设置值和CPU核心数一致
error_log /usr/local/webserver/nginx/logs/nginx_error.log crit; #日志位置和日志级别
#pid        logs/nginx.pid;

events {
	worker_connections  1024;
}

http {
	include       mime.types;
	default_type  application/octet-stream;
	
	sendfile        on;
	#tcp_nopush     on;

	#keepalive_timeout  0;
	keepalive_timeout  65;

	#gzip  on;

	server {
		listen       80; #监听端口
		server_name  localhost; #域名
		
		location / {
			root   html; #站点目录
			index  index.html index.htm;
		}
	}
}
```
