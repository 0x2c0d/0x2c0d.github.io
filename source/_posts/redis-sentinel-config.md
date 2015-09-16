title: redis sentinel 配置步骤
date: 2015-07-15 15:32:18
tags:
---
本文是对redis官方的高可用方案sentinel的配置说明和需要注意的信息。

<!--more-->

# 一 简介
sentinel 中文意思就是“哨兵”的意思。
形象的理解就是，启动了几个redis的实例，其中有些是master有些是slave
然后同时有几个哨兵负责看守。
一个哨兵可以监控1个到多个的redis实例，对于某个master需要多少个哨兵把守，可以进行配置。
# 二 搭建
以最简单的结构来确定。就是
1个master 2个slave 3个sentinel 的结构。
三个机器  
10.200.1.100  master 6379   sentinel 26379
10.200.1.101  slave 6379      sentinel 26379
10.200.1.102  slave 6379      sentinel 26379

安全起见，都要进行密码验证

假设已经编译好的目录结构是：

redis_node
|--redis-server
|--redis.conf
|--redis-sentinel
|--sentinel.conf
|--redis-cli


那么配置就分为2个步骤。
1. 配置 redis实例
2. 配置sentinel实例

为了使结构更明确，redis实例使用  redis-server , sentinel实例用 redis-sentinel

## 2.1 配置redis实例
分为master与slave的配置，配置的信息可以参考官方安装包根目录的 redis.conf文件
需要注意的是：如果要给实例配置密码，需要把每个实例的密码配置成一样的，不然的话，当发生主备切换的时候，
sentinel中配置的密码和备用的密码不一样，会导致出错。

### 2.1.1 master配置
配置的是  10.200.1.100
```bash
$cat redis.conf
port 6379  # redis-server默认端口
appendonly yes
requirepass "demopass001" # 本实例的访问密码
masterauth "demopass001" # 集群master的访问密码
```
### 2.1.2 slave配置
配置的是 10.200.1.101 和 10.200.1.102

```bash
$cat redis.conf
port 6379
slaveof 10.200.1.100 6379 #
appendonly yes
requirepass "demopass001" # 本实例的访问密码
masterauth "demopass001" # 集群master的访问密码
```

### 2.1.3 启动
在master配置文件中，可以看到是没有slave的信息的。所以启动顺序是先启动master，然后再启动所属的slave
进入redis对应的目录,然后启动redis-server

```bash
$ cd redis_node
$ ./redis-server redis.conf &
```

## 2.2 配置 sentinel实例
配置的是3台机器上的sentinel
配置的信息都是一样的

```bash
$ cat sentinel.conf
port 26379
sentinel monitor coremaster 10.200.1.100 6379 2
sentinel down-after-millseconds coremaster 5000
sentinel failover-timeout coremaster 5000
sentinel auth-pass coremaster "demopass001"

$ cd redis_node
$ ./redis-sentinel sentinel.conf &
```

分别启动3个sentinel就可以了

## 2.3 验证是否可用
 随便哪个节点

```bash
$ cd redis_node
$ ./redis-cli -p 26379

127.0.0.1:26379> sentinel masters

127.0.0.1:26379> sentinel slaves coremaster
```

可以显示出对应的信息。
