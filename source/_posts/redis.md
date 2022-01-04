---
title: Redis使用
date: 2021-12-31 14:30:00
tags: 
- Redis 
- DB
---

<!-- [命令详解](https://www.php.cn/redis/444414.html) -->

## 定义
Redis 是一个key-value 存储系统，是跨平台的非关系型数据库。
Redis 是一个开源的使用 ANSI C 语言编写、遵守 BSD 协议、**支持网络、可基于内存、分布式、可持久化**的键值对(Key-Value)存储数据库，并提供多种语言的 API。Redis 通常被称为数据结构服务器，因为值（value）可以是<font color="rgb(200, 100, 100)">**字符串(String)、哈希(Hash)、列表(list)、集合(sets)和有序集合(sorted sets)**</font>等类型。

## 优点

1. **速度快，完全基于内存，使用 C 语言实现，网络层使用 epoll 解决高并发问题，单线程模型避免了不必要的上下文切换及竞争条件；**
注意：单线程仅仅是说在网络请求这一模块上用一个请求处理客户端的请求，像持久化它就会重开一个线程/进程去进行处理。

2. 丰富的数据类型，Redis 有 8 种数据类型，当然常用的主要是 String、Hash、List、Set、 SortSet 这 5 种类型，他们都是基于键值的方式组织数据。每一种数据类型提供了非常丰富的操作命令，可以满足绝大部分需求，如果有特殊需求还能自己通过 lua 脚本自己创建新的命令（具备原子性）；除了提供的丰富的数据类型，Redis 还提供了像慢查询分析、性能测试、Pipeline、事务、Lua自定义命令、Bitmaps、HyperLogLog、**发布/订阅**、Geo 等个性化功能。

## redis适用场景

1. **排行榜**，利用 Redis 的 SortSet 数据结构能够非常方便搞定；
2. **计算器/限速器**，利用 Redis 中原子性的自增操作，我们可以统计类似用户点赞数、用户访问数等；限速器比较典型的使用场景是限制某个用户访问某个 API 的频率，常用的有抢购时，防止用户疯狂点击带来不必要的压力；
3. **好友关系**，利用集合的一些命令，比如求交集、并集、差集等，可以方便搞定一些共同好友、共同爱好之类的功能；
4. **简单消息队列**，除了 Redis 自身的发布/订阅模式，我们也可以利用 List 来实现一个队列机制，比如到货通知、邮件发送之类的需求，不需要高可靠，但是会带来非常大的 DB 压力，完全可以用 List 来完成异步解耦；
5. **Session共享**，以 PHP 为例，默认 Session 是保存在服务器的文件中，如果是集群服务，同一个用户过来可能落在不同机器上，这就会导致用户频繁登陆；采用 Redis 保存 Session 后，无论用户落在那台机器上都能够获取到对应的 Session 信息。


## redis不适用的场景

数据量太大、数据访问频率非常低的业务都不适合使用 Redis，数据太大会增加成本，访问频率太低，保存在内存中纯属浪费资源。


## 连接redis服务器
- 连接本地的redis服务：

	```shell
	$ redis-cli
	```
        
- 选择数据库：

	```shell
	$ select index
	
	127.0.0.1:6379> select 4
	OK
	127.0.0.1:6379[4]>
	```


- 连接远程的redis服务：

	```shell
	$ redis-cli -h host -p port -a password
	```
        
- 使用PING命令检测redis服务是否连接成功

        
	```	bash
	$ redis-cli
	127.0.0.1:6379>
	127.0.0.1:6379> PING
	PONG
	```
        
- 避免中文乱码：

	```bash
	$ redis-cli --raw
	```
        
## redis-cli --help
### redis-cli的两种使用模式

1. **交互模式**：用户输入redis-cli，进入redis-cli环境，输入redis命令，并获取返回结果。
2. **命令行模式**：redis操作命令作为redis-cli的参数，传递给redis-cli，结果打印在标准命令行上。


### 从其他程序获取输入

1. 使用-x选项

	```bash
	$ redis-cli -x set foo < /etc/services
	```
    上面的例子中，没指定 SET 命令的最后一个参数，但使用了-x选项，并将文件重定向到CLI的标准输入。因此，输入被读取，并用作命令的最后一个参数。


2. 另一种方法是把redis-cli要执行的一系列写入文本文件，依次执行commands.txt中的所有命令，就好像它们是由用户交互键入的一样。如果需要，可以在文件内用引号，以便可以在其中包含带空格或换行符的单个参数或其他特殊字符。

	```bash
	$ cat /tmp/commands.txt
	set foo "This is a single argument"
	strlen foo
	
	$ cat /tmp/commands.txt | redis-cli
	OK
	(integer) 25
	```
### CSV输出

使用redis-cli快速将数据从Redis导出到外部程序。这可以使用CSV（逗号分隔值）输出功能来完成：

```bash
$ redis-cli --csv lrange mylist 0 -1
```
    
### 非阻塞获取所有键列表
```bash
$ redis-cli --scan --pattern 'user:*' | wc -l
$ redis-cli --scan | head -10
```

## 基础参数
```bash
--raw和–no-raw 选项在终端上强制进行原始输出。实际上，redis-cli仅当它检测到标准输出是tty（基本上是终端）时，才会显示其他信息，提高可读性。其它情况下，它将自动启用原始输出模式，不带返回值类型。
-n <dbnum>选择特定编号的数据库，默认的数据库编号为零
-r <count>和-i <delay> 第一个参数是运行命令的次数，第二个参数配置命令调用之间的延迟（以秒为单位）（十进制数（如0.1，以表示100毫秒）。
--stat 连续统计模式实时监视Redis实例
--bigkeys 扫描大键
```

