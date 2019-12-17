---
layout: post
title: "Redis Introduction Part One"
description: "redis introducton "
tags: [redis]
---

作为读写性能出众的K-V类型数据库---Redis几乎在现在所有的项目中都会使用。常常用它来共享Session,缓存数据,或者是用它来实现一个简单的锁。
昨天简单记录了一下redis的单机版安装。之后再考虑学习一下reids集群的搭建。今天再写点简单的，算是一个笔记。

### 数据类型

redis作为key-value的非关系型数据库，我们常用的5种数据类型(string,list,set,orderd set,hash table),都是指的是value的数据类型。
当然还有其他类型的value，比如bitmap,hyperloglog,geo,stream。后面这些我平时用的少。回头再研究研究。

对redis来说，所有的key其实都是一个字符串,**实际项目中我们常常会在key的写法上做一些约束**，比如规定前缀，规定单词之间的分割符号
```
set name zhangsan    
get name # 返回zhangsan
hset student:zhangsan  math  59     # 学生张三的考试成绩就记录在 student:zhangsan 这个hash表中。 
hset student:zhangsan  music  61     # 
hget student:zhangsan  math  # 返回 59
hgetall student:zhangsan   # 返回 math  59  music 61
hdel student:zhangsan math  # 删除数学成绩

lpush  mylist 11   # mylist就是一个列表的名称，从左边添加一个元素到列表mylist中
lpush  mylist 12   # 
lpop  mylist  # 从列表mylist的左侧弹出第一个元素，返回12

sadd  myset  1  # 往集合中添加一个元素
```

```
redis可以设置key的缓存时间，默认单位是秒。redis可以给整个hash表设置缓存时间，但是不能给hash表中的某个key单独设置缓存时间
关于redis缓存过期的处理，这也可以单独记录一篇文章了。先挖个坑，以后再填吧
```
expire  mykey  12  # 缓存12秒
ttl mykey    # 可以查看剩余缓存时间
```

### 其他命令

```
https://redis.io/commands
```

### Lua
据说现在不会`Lua`都不好意思称自己会redis.这也是一个大坑，我以后再填吧

