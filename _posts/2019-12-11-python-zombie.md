---
layout: post
title: "Python generate a zobmie"
description: "python3 zombie"
tags: [python3, zombie]
---


今天分享一个简单的知识点，讲师进程---zobmie
当然，网上有了很多这类的知识讲解，本文就算是自己的一个笔记吧。

## 什么是僵尸进程？
    
    当子进程在父进程之前终止，子进程不会完全消失，否则父进程在最终检查子进程是否终止时是无法获取它的终止状态的。 内核为每一个终止的子进程保存了一些信息，所以当父进程调用wait或waitpid时, 可以得到这些信息。
    这些信息至少有进程ID、该进程的终止状态、该进程使用的CPU时间总量

    在UNIX中，一个已经终止，但是父进程尚未对其进行处理(获取子进程的有关信息、释放它们占用的资源)的进程被称为*僵尸进程*zobmie


    当然，还有一种情况，当父进程先中终止了，它的子进程们会怎样呢？答案是，这些子进程的父进程会变为init进程(进程ID为1)。当这些子进程再终止时，init进程会调用一个wait获取其终止状态，从而不会产生僵尸进程

## 如何发现僵尸进程？
    几个方法：
    1.top命令, `zombie` 前面的数字就是僵尸进程的数量
    ```
	top - 23:48:32 up 14 days,  1:53,  6 users,  load average: 0.28, 0.34, 0.42                                                                       Tasks: 304 total,   1 running, 254 sleeping,   0 stopped,   0 zombie                                                                              %Cpu(s):  1.2 us,  0.7 sy,  0.0 ni, 98.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st                                                                   KiB Mem :  3729440 total,   187164 free,  2335108 used,  1207168 buff/cache
    KiB Swap:  2097148 total,   948220 free,  1148928 used.   730932 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
    31730 wm        20   0  931404 241468 108284 S   3.3  6.5   1:38.39 chrome
    2364 wm        20   0 3959488 237336  70132 S   2.0  6.4 131:42.83 gnome-shell
	 1422 gdm       20   0 3488680  48252  19776 S   0.7  1.3   5:45.24 gnome-shell
    ```
    2. `ps -ef | grep defunct`

## 如何手动产生一个僵尸进程？

如下所示，子进程终止后，父进程并未wait子进程，而是直接sleep了。这个时候，就会出现zombia进程了。
```
from multiprocessing import Process
import time, os

def run():
    print("son",os.getpid())


if __name__ == '__main__':
    p = Process(target=run)
    p.start()

    print("father",os.getpid())
    time.sleep(1000)
```

输出如下：
```
('father', 32687)
('son', 32688)
```
此时top命令上`1 zombie`,
ps命令如下
wm       32695 32694  0 00:03 pts/2    00:00:00 [python] <defunct>




## 如何干掉一个僵尸进程？

一般情况下，`kill -9 pid`是不能干掉僵尸进程。可以通过kill掉僵尸进程的父进程,来阻止其他僵尸进程产生
