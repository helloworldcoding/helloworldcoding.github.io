---
layout: post
title: "Python3 use socket connect to redis "
description: "Python3 use socket connect to redis without third library "
tags: [python3, redis,socket]
---

今天想了解一下，如何用python连接到redis。当然，可以用`redis-py`这个现成的包，只需简单的`pip install redis`就可以很愉快地用操作redis了
比如：
```
>>> import redis
>>> r = redis.Redis(host='localhost', port=6379, db=0)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
'bar'
```

**然而，作为一个喜欢折腾的程序员** 我肯定不满足使用工具，我想了解一下这是如何实现的。
翻了一下`redis-py`的源码，其实就是`socket`，于是就想造个简单的轮子---直接通过socket连接到redis
顺代了解一下socket编成的基本知识和`redis-py`的源码


## socket的历史

套接字起源于 20 世纪 70 年代加利福尼亚大学伯克利分校版本的 Unix,即人们所说的 BSD Unix。 因此,有时人们也把套接字称为“伯克利套接字”或“BSD 套接字”。一开始,套接字被设计用在同 一台主机上多个应用程序之间的通讯。这也被称进程间通讯或IPC。套接字有两种（或者称为有两个种族）,分别是基于文件型的和基于网络型的。 

基于文件类型的套接字家族
    - 套接字家族的名字：AF_UNIX
    unix一切皆文件，基于文件的套接字调用的就是底层的文件系统来取数据，两个套接字进程运行在同一机器，可以通过访问同一个文件系统间接完成通信

基于网络类型的套接字家族
    - 套接字家族的名字：AF_INET
    (还有AF_INET6被用于ipv6，还有一些其他的地址家族，不过，他们要么是只用于某个平台，要么就是已经被废弃，或者是很少被使用，或者是根本没有实现，所有地址家族中，AF_INET是使用最广泛的一个，python支持很多种地址家族，但是由于我们只关心网络编程，所以大部分时候我么只使用AF_INET)

套接字把复杂的TCP/IP协议族隐藏在Socket接口后面，对用户来说，一组简单的接口就是全部，让Socket去组织数据，以符合指定的协议。所以，我们无需深入理解tcp/udp协议，socket已经为我们封装好了，我们只需要遵循socket的规定去编程，写出的程序自然就是遵循tcp/udp标准的

简直是一把利器(书架上的《Unix网络编程》卷一：套接字联网API在召唤我

## socket基础使用方法

- **服务端**
```
import socket
import sys

# 创建 socket 对象
serversocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM) 

# 获取本地主机名
host = socket.gethostname()
port = 9999
# 绑定端口号
serversocket.bind((host, port))
# 设置最大连接数，超过后排队
serversocket.listen(5)
while True:
    # 建立客户端连接
    clientsocket,addr = serversocket.accept()      
    print("连接地址: %s" % str(addr))
    msg='欢迎关注匿名程序媛！'+ "\r\n"
    clientsocket.send(msg.encode('utf-8'))
    clientsocket.close()
```
- **客户端**
```
import socket
import sys
# 创建 socket 对象
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM) 
# 获取本地主机名
host = socket.gethostname() 
# 设置端口号
port = 9999
# 连接服务，指定主机和端口
s.connect((host, port))
# 接收小于 1024 字节的数据
msg = s.recv(1024)
s.close()
print (msg.decode('utf-8'))
```

**服务端套接字函数**

> s.bind()    #绑定(主机,端口号)到套接字
> s.listen()  #开始TCP监听
> s.accept()  #被动接受TCP客户的连接,(阻塞式)等待连接的到来
 

**客户端套接字函数** 
> s.connect()     #主动初始化TCP服务器连接
> s.connect_ex()  #connect()函数的扩展版本,出错时返回出错码,而不是抛出异常
 

**公共用途的套接字函数(客户端和服务端都能使用)**
> s.recv()            #接收TCP数据
> s.send()            #发送TCP数据(send在待发送数据量大于己端缓存区剩余空间时,数据丢失,不会发完)
> s.sendall()         #发送完整的TCP数据(本质就是循环调用send,sendall在待发送数据量大于己端缓存区剩余空间时,数据不丢失,循环调用send直到发完)
> s.recvfrom()        #接收UDP数据
> s.sendto()          #发送UDP数据
> s.getpeername()     #连接到当前套接字的远端的地址
> s.getsockname()     #当前套接字的地址
> s.getsockopt()      #返回指定套接字的参数
> s.setsockopt()      #设置指定套接字的参数
> s.close()           #关闭套接字


所以？如何连接到redis呢?

## my-socket-connect-to-redis 
```
#!/usr/bin/python3

import socket
'''
python3 连接redis
'''

host = '127.0.0.1'
port = 6379
buf_size = 1
conn = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
conn.connect((host, port))

cmd = 'SELECT 2\n'.encode('utf-8')
r = conn.sendall(cmd)


cmd = 'PING\n'.encode('utf-8')
conn.sendall(cmd)
while True:
    res = conn.recv(buf_size)
    print(res)
    if not res:
        break
conn.close()
```
执行一下`python3 ./redis_client.py`,就可以得到如下返回。
```
b'+'
b'O'
b'K'
b'\r'
b'\n'
b'+'
b'P'
b'O'
b'N'
b'G'
b'\r'
b'\n'
```
我们连接到第二个数据库，然后`ping`了一下，看到redis返回了`PONG` ^_^



