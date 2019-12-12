---
layout: post
title: "Python generate a zobmie"
description: "python3 zombie"
tags: [python3, zombie]
---


今天分享一个简单的知识点，僵尸进程---zobmie
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
	top - 23:48:32 up 14 days,  1:53,  6 users,  load average: 0.28, 0.34, 0.42
    Tasks: 304 total,   1 running, 254 sleeping,   0 stopped,   0 zombie
    %Cpu(s):  1.2 us,  0.7 sy,  0.0 ni, 98.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    KiB Mem :  3729440 total,   187164 free,  2335108 used,  1207168 buff/cache
    KiB Swap:  2097148 total,   948220 free,  1148928 used.   730932 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
    31730 mm        20   0  931404 241468 108284 S   3.3  6.5   1:38.39 chrome
    2364 mm        20   0 3959488 237336  70132 S   2.0  6.4 131:42.83 gnome-shell
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
father, 5607
son, 5608
```
此时top命令上会出现`1 zombie`,
ps命令如下
```
UID        PID  PPID  C STIME TTY          TIME CMD
mm       5608 5607  0 00:03 pts/2    00:00:00 [python] <defunct>
```

## 如何干掉一个僵尸进程？

一般情况下，`kill -9 pid`是不能干掉僵尸进程。要找到僵尸进程的父进程,分析产生僵尸进程的原因,从根本上杜绝僵尸进程的原因.
可以通过kill掉僵尸进程的父进程,来阻止其他僵尸进程产生.


## 查看一下这个僵尸进程的详细信息

`cat /proc/5608/status` 当然可用这个命令查看其他进程的信息.只要替换一下进程id就好了

Name:	python3
State:	Z (zombie)
Tgid:	5608
Ngid:	0
Pid:	5608
PPid:	5607
TracerPid:	0
Uid:	1000	1000	1000	1000
Gid:	1000	1000	1000	1000
FDSize:	0
Groups:	4 24 27 30 46 116 126 1000 
NStgid:	5608
NSpid:	5608
NSpgid:	5607
NSsid:	11104
Threads:	1
SigQ:	0/14395
SigPnd:	0000000000000000
ShdPnd:	0000000000000000
SigBlk:	0000000000000000
SigIgn:	0000000001001000
SigCgt:	0000000180000002
CapInh:	0000000000000000
CapPrm:	0000000000000000
CapEff:	0000000000000000
CapBnd:	0000003fffffffff
CapAmb:	0000000000000000
NoNewPrivs:	0
Seccomp:	0
Speculation_Store_Bypass:	thread vulnerable
Cpus_allowed:	ff
Cpus_allowed_list:	0-7
Mems_allowed:	00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000001
Mems_allowed_list:	0
voluntary_ctxt_switches:	1
nonvoluntary_ctxt_switches:	0

部分字段解释
```
VmPeak: 代表当前进程运行过程中占用内存的峰值.
VmSize: 代表进程现在正在占用的内存
VmLck: 代表进程已经锁住的物理内存的大小.锁住的物理内存不能交换到硬盘.
VmHWM: 是程序得到分配到物理内存的峰值.
VmRSS: 是程序现在使用的物理内存.
VmData: 表示进程数据段的大小.
VmStk: 表示进程堆栈段的大小.
VmExe: 表示进程代码的大小.
VmLib: 表示进程所使用LIB库的大小.
VmPTE: 占用的页表的大小.
VmSwap: 进程占用Swap的大小.
Threads: 表示当前进程组的线程数量.
SigPnd: 屏蔽位,存储了该线程的待处理信号,等同于线程的PENDING信号.
ShnPnd: 屏蔽位,存储了该线程组的待处理信号.等同于进程组的PENDING信号.
SigBlk: 存放被阻塞的信号,等同于BLOCKED信号.
SigIgn: 存放被忽略的信号,等同于IGNORED信号.
SigCgt: 存放捕获的信号,等同于CAUGHT信号.
CapEff: 当一个进程要进行某个特权操作时,操作系统会检查cap_effective的对应位是否有效,而不再是检查进程的有效UID是否为0.
CapPrm: 表示进程能够使用的能力,在cap_permitted中可以包含cap_effective中没有的能力，这些能力是被进程自己临时放弃的,也可以说cap_effective是cap_permitted的一个子集.
CapInh: 表示能够被当前进程执行的程序继承的能力.
CapBnd: 是系统的边界能力,我们无法改变它.
Cpus_allowed: 指出该进程可以使用CPU的亲和性掩码,如果是两块CPU,所以这里就是3,如果该进程指定为4个CPU(如果有话),这里就是F(1111).
Cpus_allowed_list: 0-7指出该进程可以使用CPU的列表,这里是0-7.
voluntary_ctxt_switches: 表示进程主动切换的次数.
nonvoluntary_ctxt_switches: 表示进程被动切换的次数.
```
