---
layout: post
title: "「大数据学习」1. 数据库Redis基础命令"
subtitle: '只要你肯坚持，才会体会到放弃的快乐'
author: "叉叉敌"
header-style: text
tags:
  - redis
  - 大数据
---



![](https://gitee.com/chasays/mdPic/raw/master/uPic/LFKvwa.jpg)


## 由来

数据结构的服务器，
引用官方的一段话。
>REmote DIctionary Server(Redis) 是一个由Salvatore Sanfilippo写的key-value存储系统。
Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。
它通常被称为数据结构服务器，因为值（value）可以是 字符串(String), 哈希(Hash), 列表(list), 集合(sets) 和 有序集合(sorted sets)等类型。

--- 
#### Redis 是完全开源的，遵守 BSD 协议，是一个高性能的 key-value 数据库。

Redis 与其他 key - value 缓存产品有以下三个特点：

- Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
- Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
- Redis支持数据的备份，即master-slave模式的数据备份。


优势和之前的MongoDB类似， `就是快，还支持多种数据类型，已经原子操作属性。`

## 安装

macos 直接用 brew 安装即可。
```sh
brew install redis
```

![1](https://gitee.com/chasays/mdPic/raw/master/uPic/HgV4mz.png)


windows 的可以用 github提供编译好的文件
>https://github.com/tporadowski/redis/releases

其他可以用源码直接编译后添加到环境变量里面
>https://redis.io/download


然后输入`redis-server`，输出下面就是安装成功。
![](https://gitee.com/chasays/mdPic/raw/master/uPic/zMdhgk.png)

再开一个tab输入 `redis-cli`

![](https://gitee.com/chasays/mdPic/raw/master/uPic/wMM0Ye.png)

-----
### 配置

可以通过`config`来查看或者修改配置。

> 可以输入conf<tab>自动联想自动补全

- 查看log等级
```
127.0.0.1:6379> CONFIG GET loglevel
1) "loglevel"
2) "notice"
```

- 查看所有配置
```
redis 127.0.0.1:6379> CONFIG GET *

  1) "dbfilename"
  2) "dump.rdb"
  3) "requirepass"
 ....
```

- 设置配置
```
127.0.0.1:6379> CONFIG SET loglevel warning
OK
127.0.0.1:6379> CONFIG GET loglevel
1) "loglevel"
2) "warning"
127.0.0.1:6379>
```
其他设置，常见的 `port， daemonize， bind， timeout， logfile， databases`。


-----
## 数据类型

>Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。

![](https://gitee.com/chasays/mdPic/raw/master/uPic/ZUi3W4.png)

----
1.  string

这个是最基础的类型，也是二进制的，因此只要可以转化二进制的都可以保存。`set 和get。`
```
127.0.0.1:6379> set chasays "学习redis"
OK
127.0.0.1:6379> get chasays
"\xe5\xad\xa6\xe4\xb9\xa0redis"
127.0.0.1:6379>
```

> 上面这个用utf8解码即可。启动`redis-cli --raw`

```
(base) ➜  ~ redis-cli --raw
127.0.0.1:6379>
127.0.0.1:6379> get chasays
学习redis
127.0.0.1:6379>
```
----
2. hash 存储

>Redis hash 是一个键值(key=>value)对集合。`HMSET， HMGET`

```
127.0.0.1:6379> HMSET test field1 "Hello" field2 "World"
OK
127.0.0.1:6379> HMGET test field1
Hello
127.0.0.1:6379> HMGET test field2
World
```
每个 hash 可以存储 2^32 -1 键值对（40多亿）。


----
3. list 列表

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。用到`lpush， lrange`

```
127.0.0.1:6379> LPUSH test 1
1
127.0.0.1:6379> LPUSH test 2
2
127.0.0.1:6379> LRANGE test 0 4
2
1
```


----
5. set 集合

Redis 的 Set 是 string 类型的无序集合。
>集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。有2个函数要掌握`sadd和smembers`。



```
127.0.0.1:6379> sadd test 1
1
127.0.0.1:6379> sadd test 2
1
127.0.0.1:6379> SMEMBERS test
1
2
```

>注意set里面的值是唯一的， sadd同一个value，是不会重复的。

---
6. zset 有序集合
Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
>不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

```
127.0.0.1:6379> zadd test 1 a
1
127.0.0.1:6379> ZADD test 2 b
1
127.0.0.1:6379> zadd test 1 c
1

127.0.0.1:6379> ZRANGEBYSCORE test 0 4
a
c
b
```

--- 
## 总结
- string 二进制，可以包含任意对象
- hash 场景适合存储，读取，修改用户属性。
- list 是一个链表，增删时间复杂度低，因此用于消息列队，删除等
- set  添加、删除,查找的复杂度都是O(1)， 适用于唯一性，比如ip等
- zsorted 会进行排序，这个可以用于排行榜等。


后面接着学习Redis的命令脚本和高级技术。



>[github博客](https://chasays.github.io/)
>微信公众号：chasays， 欢迎关注一起吹牛逼，也可以加微信号「xxd_0225」互吹。



