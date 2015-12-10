title: SSDB：高性能数据库服务器
date: 2015-3-17 16:03:01
categories: 日薪越亿
tags: [NoSql, SSDB]
---
## 介绍

SSDB是一个开源的[高性能数据库服务器](http://ssdb.io/zh_cn/), 使用Google LevelDB作为存储引擎, 支持T级别的数据, 同时支持类似Redis中的zset和hash等数据结构, 在同时需求高性能和大数据的条件下, 作为Redis的替代方案.

因为SSDB的最初目的是替代Redis, 所以SSDB会经常和Redis进行比较. 我们知道, Redis是经常的”主-从”架构, 虽然可以得到负载均衡以及数据跨地域备份的功能, 但无法实现高可用性. 考虑这种情况, Redis的主和从分别在两个IDC机房, 当主所在的机房出现故障时, 整个服务其实就相当于停止了. 因为所有写操作都失败, 而应用一般不会实现自动降级服务.

而SSDB支持”双主”架构(SSDB分布式架构: https://github.com/ideawu/ssdb/wiki/Replication), 两个或者更多的主服务器. 当其中一部分出现故障时, 剩余的主服务器仍然能正常接受写请求, 从而保证服务正常可用, 再将DNS解析修改之后, 就能在机房故障后立即恢复100%可用.

SSDB 被开发和开源出来后, 已经在生产环境经受了3个季度的考验,SSDB最先在”IT牛人博客聚合网站“进行尝试应用, 接着在360游戏部门得到大规模应用, 目前支撑的数据量已经达到数百G. 这些应用最初是使用Redis的, 迁移到SSDB的成本非常低, 涉及的代码改动极小.

## 使用

### 安装
SSDB 的建议安装方式是源码编译安装, 建议运行环境是主流 Linux 发行版. 远程 SSH 登录你的服务器, 然后⽤用下⾯面的命令下载、编译、安装和运行:

``` bash
[sphenginx@~]$ sudo wget --no-check-certificate https://github.com/ideawu/ssdb/archive/master.zip
[sphenginx@~]$ sudo unzip master
[sphenginx@~]$ cd ssdb-master
[sphenginx@~]$ sudo make
[sphenginx@~]$ #optional, install ssdb in /usr/local/ssdb
[sphenginx@~]$ sudo make install
# start master
[sphenginx@~]$ sudo ./ssdb-server ssdb.conf
# or start as daemon
[sphenginx@~]$ sudo ./ssdb-server -d ssdb.conf
# ssdb command line
[sphenginx@~]$ sudo ./ssdb-cli -p 8888
# stop ssdb-server
[sphenginx@~]$ sudo kill `cat ./var/ssdb.pid`
```
SSDB 默认安装在 `/usr/local/ssdb` 目录下. ssdb-server 是服务器的程序, ssdb-cli 是命令行客户端.

### 配置

SSDB配置文件: [http://www.ideawu.net/blog/archives/733.html](http://www.ideawu.net/blog/archives/733.html)

SSDB  附带的 ssdb.conf 你不用修改便可以使用. 如果你要高度定制, 还是需要修改一些配置的. 下面做介绍. SSDB 的配置文件是一种层级 key-value 的静态配置文件, 通过一个 TAB 缩进来表示层级关系. 以 ‘#’ 号开始的行是注释. 标准的配置文件如下:

``` bash
# ssdb-server config 
# MUST indent by TAB!

# relative to path of this file, directory must exists 
work_dir = ./var 
pidfile = ./var/ssdb.pid

server: 
        ip: 127.0.0.1 
        port: 8888 
        # bind to public ip 
        #ip: 0.0.0.0 
        # format: allow|deny: all|ip_prefix 
        # multiple allows or denys is supported 
        #deny: all 
        #allow: 127.0.0.1 
        #allow: 192.168

replication: 
        slaveof: 
                # to identify a master even if it moved(ip, port changed) 
                # if set to empty or not defined, ip:port will be used. 
                #id: svc_2 
                # sync|mirror, default is sync 
                #type: sync 
                #ip: 127.0.0.1 
                #port: 8889

logger: 
        level: info 
        output: log.txt 
        rotate: 
                size: 1000000000

leveldb: 
        # in MB 
        cache_size: 500 
        # in KB 
        block_size: 32 
        # in MB 
        write_buffer_size: 64 
        # in MB 
        compaction_speed: 1000 
        # yes|no 
        compression: no
```
__work_dir__: ssdb-server 的工作目录, 启动后, 会在这个目录下生成 data 和 meta 两个目录, 用来保存 LevelDB 的数据库文件. 这个目录是相对于 ssdb.conf 的相对路径, 也可以指定绝对路径.

__server__: ip 和 port 指定了服务器要监听的 IP 和端口号. 如果 ip 是 0.0.0.0, 则表示绑定所有的 IP. 基于安全考虑, 可以将 ip 设置为 127.0.0.1, 这样, 只有本机可以访问了. 如果要做更严格的更多的网络安全限制, 就需要依赖操作系统的 iptables.

__replication__: 用于指定主从同步复制. slaveof.ip, slaveof.port 表示, 本台 SSDB 服务器将从这个目标机上同步数据(也即这个配置文件对应的服务器是 slave). 你可以参考 ssdb_slave.conf 的配制.

__logger__: 配置日志记录. level 是日志的级别, 可以是 trace|debug|info|error. output 是日志文件的名字, SSDB 支持日志轮转, 在日志文件达到一定大小后, 将 log.txt 改名, 然后创建一个新的 log.txt.

__leveldb__: 配置 LevelDB 的参数. 你一般想要修改的是 cache_size 参数, 用于指定缓存大小. 适当的缓存可以提高读性能, 但是过大的缓存会影响写性能.

### 使用
在使用自带的 ssdb.conf 配置文件时, SSDB 生成的日志文件按体积进行分割, 仅此而已. 所以, 你需要编写自己的 crontab 进行日志压缩和定期清理. 
如果出现服务器掉电, kernel panic 等系统故障, 在系统重新启动之后, 你需要⼿手动删除 ssdb的 PID 文件 ssdb.pid, 然后才能启动 ssdb-server.另外, 你可以参考下面的做法, 在系统启动和关机时, 启动和关闭 ssdb-server: 

``` bash
# /bin/sh 
# 
# chkconfig:345 98 98 
# description: SSDB is a fast NoSQL database for storing big list of billions of elements 
# processname:ssdb

case "$1" in 
  'start') 
    /usr/local/ssdb/ssdb-server -d /usr/local/ssdb/ssdb.conf 
    echo "ssdb started." 
    ;; 
  'stop') 
    kill `cat /usr/local/ssdb/var/ssdb.pid` 
    echo "ssdb stopped." 
    ;; 
  'restart') 
    kill `cat /usr/local/ssdb/var/ssdb.pid` 
    echo "ssdb stopped." 
    sleep 0.5 
    /usr/local/ssdb/ssdb-server -d /usr/local/ssdb/ 
ssdb.conf 
    echo "ssdb started." 
    ;; 
  *) 
    echo "Usage: $0 {start|stop|restart}" 
    exit 1 
  ;; 
esac

exit 0
```

把文件保存为 /etc/init.d/ssdb.sh(需要 root 权限), 然后执行: 
``` bash
[sphenginx@~]$ chmod ugo+x /etc/init.d/ssdb.sh
```
把 ssdb加入chkconfig，并设置开机启动。
``` bash
[sphenginx@~]$ sudo chkconfig --add ssdb.sh 
[sphenginx@~]$ chkconfig ssdb.sh on
```
启动、停止的命令如下:
``` bash
[sphenginx@~]$ sudo service ssdb.sh stop 
ssdb stopped. 
[sphenginx@~]$ sudo service ssdb.sh start 
ssdb 1.6.7 
Copyright (c) 2012-2013 ideawu.com
ssdb started.
```

### Win platform

项目代码中已经加入PHP 的api，API地址：[http://ssdb.io/docs/zh_cn/php/index.html](http://ssdb.io/docs/zh_cn/php/index.html)
也可以安装一下SSDB的管理软件：[PSA](https://github.com/ssdb/phpssdbadmin) 
另外, SSDB 提供了预编译的 Windows 下的可执行安装包, Windows 用户可以下载后直接运行 ssdb-server.exe. Windows 下的 SSDB 依赖 cygwin, 所以附带了几个 dll 文件. 使用方式:

1. 从 https://github.com/ideawu/ssdb-bin 下载可执行文件 ssdb-server.exe 和相关 dll.  
2. 从 https://github.com/ideawu/ssdb 下载 ssdb.conf 配置文件.  
3. 解压, 然后从开始菜单中运行 cmd.exe.  
4. 在 cmd.exe 启动后, cd ssdb-server.exe 所在的目录.  
5. 执行 ssdb-server.exe ssdb.conf  

## More info

SSDB开源数据库项目地址: [https://github.com/ideawu/ssdb](https://github.com/ideawu/ssdb)
作者博客地址： [http://www.ideawu.net/blog/ssdb](http://www.ideawu.net/blog/ssdb)