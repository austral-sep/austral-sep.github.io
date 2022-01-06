---
title: systemd-journald系统服务疯狂占用内存和CPU
date: 2021-12-29 16:39:32
tags: journald
categoris:
- Linux
---
## 原因
systemd-journald是Linux下的日志系统。该日志系统默认将日志保存在内存里的/run/log/journal文件夹中。这样将占用大量内存，造成系统资源紧张，进程之间相互夺取资源，从而导致系统不稳定。解决方法是创建立/var/log/journal文件夹，并重启journald服务即可。

## 解决方法
```bash
$ mkdir /var/log/journal # 将日志保存在磁盘中，需手动创建/var下的目录
$ vim /etc/systemd/journald.conf # 编辑journald.conf配置文件
```

```config
[Journal]
RateLimitBurst=100
SystemMaxUse=50M
RuntimeMaxUse=5M
ForwardToSyslog=no
MaxLevelStore=warning
MaxLevelSyslog=warning
MaxLevelKMsg=warning
```

```bash
$ systemctl restart systemd-journald.service # 重启journald服务
$ du -h /run/log/journal # 检查内存中日志是否变为0M
```

## 常用命令

### 日志信息筛选
```bash
$ journalctl -f # 类似 tail -f 功能，持续显示journal日志内容
$ journalctl -r # 反向输出，显示最新的输出到最旧的输出
$ journalctl -n 20 # 显示最新的20条日志
$ journalctl -p warning # 按信息严重等级显示
$ journalctl --since yesterday --unitl today # 设置开始与结束时间，只显示某段时间范围内的数据
$ _SYSTEMD_UNIT=unit.service：只输出某个服务的信息
$ _COMM=bash：只输出与 bash 有关的信息
$ _PID=pid：只输出某个 PID 的信息
$ _UID=uid：只输出某个 uid 的信息
$ SYSLOG_FACILITY=[0-23]：使用 syslog.h 规范的服务相对应的序号来显示指定数据
```

### 日志信息管理
```bash
$ journalctl --disk-usage # 检查当前journald使用的磁盘量
$ journalctl --vacuum-time=2d # 清除2天前的日志信息
$ journalctl --vacuum-size=10M # 只保留10M日志容量
$ journalctl --verify # 检查日志文件是否完整无损坏
```