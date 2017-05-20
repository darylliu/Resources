---
title: Redis学习1之快速入门
date: 2016-09-08 19:38:30
tags: Redis
categories: 专题
---

# Redis学习1之快速入门

## Redis简介

redis是一个高性能的key-value数据库（可以理解成一个hashmap）

<!--more-->

## Redis安装

下载地址：[https://github.com/MSOpenTech/redis/releases](https://github.com/MSOpenTech/redis/releases)

将下载好的压缩包(**Redis-x64-3.2.100.zip**)解压到某目录下(**D:\ redis**)

打开cmd窗口，进入该目录

运行  **redis-server.exe redis.windows.conf**

**<font color = red>为了方便起见，也可以将其路径加入到环境变量当中</font>**

![](/img/Redis学习1之快速入门/redis1.png)

出现如上图所示，则服务器启动成功。

当前窗口不要关闭，打开另一个新的窗口，进入该目录

运行  **redis-cli.exe -h 127.0.0.1 -p 6379**

![](/img/Redis学习1之快速入门/redis2.png)

出现如上图所示，则客户端启动成功。

下面可以先跑一个例子测试一下
存入一个新的键值对，**set key test **(key 是键，test是值)
然后再取出， **get key**

![](/img/Redis学习1之快速入门/redis3.png)

## Redis 配置

可以通过输入命令**config get * **获得当前所有配置
如果希望能够对某一项配置进行设置的话，可以输入命令** config set key**(key 为配置项的名称)

例如可以通过**config get bink**获取绑定的ip地址

![](/img/Redis学习1之快速入门/redis4.png)


## Redis 数据类型

redis 支持五种数据类型
1. string   字符串
2. hash  哈希
3. list   列表
4. set   集合
5. sorted set   有序集合


### string

sring 数据类型是最基本的数据类型

```
redis 127.0.0.1:6379> SET name "root"
OK
redis 127.0.0.1:6379> GET name
"root"
```

一般使用**set** 和**get **命令对其进行操作

### hash

hash 数据类型一般用于存储对象，它的Key 可能是一个人的姓名， 而value则是一个人的基本信息，包括性别，年龄，电话号码等等

```
127.0.0.1:6379>  HMSET student sex "male" age 10 phone 123456
OK
127.0.0.1:6379>  HGETALL student
1) "sex"
2) "male"
3) "age"
4) "10"
5) "phone"
6) "123456"
```

我们可以通过使用**HMSET** 一次性把所有的属性添加至key所对应的哈希表中
然后通过**HGETALL**一次性把所有的属性从哈希表中取出
也可以单独使用
**HGET KEY FIELD** 单独获取某个哈希表的某一项
**HSET KEY FIELD VALUE ** 单独设置某个哈希表的某一项

### list

list 数据类型是一个简单的列表，按照用户插入的顺序进行排序

```
redis 127.0.0.1:6379> LPUSH students “bob”
(integer) 1
redis 127.0.0.1:6379> LPUSH students “alice”
(integer) 2
redis 127.0.0.1:6379> LPUSH students “jack”
(integer) 3
redis 127.0.0.1:6379> LRANGE students 0 10

1) "jack"
2) "alice"
3) "bob"
```


在上述例子中，我们使用**LPUSH KEY VALUE** 将新值插入到列表头部，然后使用**LRANG KEY START STOP** 来输出一定下标范围内的所有值

我们还可以使用
**LPOP key**  移出并获取列表的第一个元素
**RPUSHX key value**  在列表的末尾添加一个新的元素
**RPOP key**  移除并获取列表最后一个元素


### set

set是string类型的无序集合，既然是集合，那么其中的元素则必然是唯一的，不能出现重合的元素，如果添加了重复的元素进去，不会报错

```
redis 127.0.0.1:6379> SADD student 1
(integer) 1
redis 127.0.0.1:6379> SADD student “new”
(integer) 1
redis 127.0.0.1:6379> SMEMBERS student

1) "new"
2) "1"
```

我们使用**SADD key member** 添加一个元素
并使用**SMEMBERS key**　来获取所有集合中的元素

### sorted set

sorted set 有序集合和普通的集合一样，都是不允许有重复的元素存在，但是不一样的是，有序集合中的元素都会关联一个数值，redis通过该值为集合中的元素进行排序

```
redis 127.0.0.1:6379> ZADD student 5 bob
(integer) 1
redis 127.0.0.1:6379> ZADD student 2 alice
(integer) 1
redis 127.0.0.1:6379> ZADD student 7 jack
(integer) 1
redis 127.0.0.1:6379> ZRANGE runoobkey 0 10 WITHSCORES

1) "alice"
2) "2"
3) "bob"
4) "5"
5) "jack"
6) "7"
```

以上例子中，我们通过**ZADD key score member **来为一个有序集合添加一个元素并绑定一个分数，并通过**ZRANGE key start stop WITHSCORES** 来通过下边范围获取相应的元素






