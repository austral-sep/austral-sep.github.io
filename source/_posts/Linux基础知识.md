---
title: Linux基础知识
date: 2021-12-31 16:00:00
tag: Linux
---


# 字节对齐 
    操作系统并非一个字节一个字节访问内存，而是按2，4，8这样的字长来访问。 因此，当CPU从存储器读数据到寄存器，IO的数据长度通常是字长。 如32位系统访问粒度是4字节(bytes), 64位系统的是8字节。 当被访问的数据长度为n字节且该数据地址为n字节对齐时，那么操作系统就可以高效地一次定位到数据， 无需多次读取，处理对齐运算等额外操作。 数据结构应该尽可能地在自然边界上对齐。如果访问未对齐的内存，CPU需要做两次内存访问。
```c
struct yikou_s
{
double d;
char c;
int i;
} yikou_t;
sizeof(yikou_t) = 16
```
```c
struct yikou_s
{
char c;
double d;
int i;
} yikou_t;
sizeof(yikou_t) = 24
```
```c
#pragma pack(4)
struct yikou_s
{
char c;
double d;
int i;
} yikou_t;
sizeof(yikou_t) = 16
```
# 大端：高尾端，小端：低尾端。
大小端模式各有优势：小端模式强制转换类型时不需要调整字节内容，直接截取低字节即可；大端模式由于符号位为第一个字节，很方便判断正负。

# 解引用
在 C 语言中通过运算符 \* 就可以拿到一个指针所指地址的内容了



# /etc/init.d 

 里面包含了一系列系统里面服务的开启和停止的脚本。
 这些系统系统服务像一个高效率的机器一样在这些目录间共同工作。有时候你想在不使用kill或者killall进程的情况下，干净的启动或者停止一个脚本的时候，inin.d这个目录就派上用场了。
 
 `/etc/init.d/command OPTION`
 
### /etc/rc#.d

当你查看/etc目录时，你会发现许多rc#.d形式的目录。（#代表数字0-6，指相对的初始化水平）这些目录中包含许多进程的控制脚本。这些脚本以’K’或者’S’开头。所有以’K’开头的脚本先于以’S’开头的脚本运行。这些脚本所在位置将决定这些脚本启动的时间。其中的脚本只是init.d脚本的链接。

### /etc/rc.local 

里面脚本是在系统初始化级别的脚本运行之后再执行的

# 重定向操作符（管道命令）：
| 重定向操作符 | 说明 | 
|:-:|-|
|>|将命令输出写入到文件或设备（例如打印机）中，而不是写在命令提示符窗口中。|
|<|从文件中而不是从键盘中读入命令输入。|
|>>|将命令输出添加到文件末尾而不删除文件中的信息。|
|>&|将一个**句柄**的输出写入到另一个句柄的输入中。|
|<&|从一个**句柄**读取输入并将其写入到另一个句柄输出中。|
|\||从一个命令中读取输出并将其写入另一个命令的输入中。也称作管道。|


标准输入0 标准输出1 标准错误输出2
2>&1: 将标准错误输出重定向到标准输出

# 设置静态ip

**1. 把命令加入到/etc/rc.local**

    ip addr add 192.168.26.74/32 dev eth1

or

    PATH=/sbin:/bin:/usr/sbin:/usr/bin 
    ifconfig eth0 192.168.1.115 netmask 255.255.255.0 
    route add default gw 192.168.1.1
    echo "nameserver 114.114.114.114" > /etc/resolv.conf

**2. 高可用双机倒换后进行虚拟ip的增加**
**3. 在/etc/sysconfig/network-scripts添加网卡名字**
* 配置eth0网卡  `vim ifcfg-eth0`

        
        
  
        DEVICE=eth2
        HWADDR=00:0C:29:8E:06:49
        TYPE=Ethernet
        ONBOOT=yes
        BOOTPROTO=static
        IPADDR=192.168.113.110
        NETMASK=255.255.255.0
        GATEWAY=192.168.113.245
        DNS1=114.114.114.114
        DNS2=8.8.8.8



* 配置文件保存后重启网络服务

        service network restart
        
# 网络配置工具
> ifconfig: interface config
> ip addr 
> iwlist: wireless
> iwconfig


列出无线网卡可以接入的热点

    iwlist wlan0 scan | grep SSID

netstat 的命令用来显示网络状态

    