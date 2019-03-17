---
layout: post
title: "Thread basic" 
tags: [c,thread,pthread_create]
description: "little things about thread" 
---

thread & process
===

> 1. 线程(thread)和进程(process)的关系，一个进程可以创建多个线程，或多个进程。
> 2. thread共父进程的内存空间,而子进程则完全复制父进程。
> 3. 线程对父进程的变量的修改会相互影响，而子进程不会

### 创建一个线程

thread1.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>  // 多线程，需要引入该头文件


void* test(void* args) {
	char* name = (char*) args; // void* 强制转化为字符串
	printf("hello %s \n",name);
	return NULL;
}

int main() {
	pthread_t td; // 申明一个线程变量
	pthread_create(&td,NULL, test,"zhangsan"); // 创建一个线程，该线程会执行test这个方法，并传入参数zhangsan
	pthread_join(td,NULL); // 主进程等待线程执行完毕
	return 0;
}

```

编译代码
	
	gcc thread1.c -lpthread -o thread1
	./thread1   

### 多线程demo

```c

#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

#define NUM 10
int arr[NUM];

// 定义个类型myargs 这个类型和int float一样，
// 只不过一个变量定义为int，就只能赋值为整型
// 而某个变量定义为myargs，则需要赋值为一个结构体
typedef struct {
	int start;
	int end;
	int sum;
}myargs ;

void* test(void* args) {
	char* name = (char*) args; // void* 强制转化为字符串
	printf("hello %s \n",name);
	return NULL;
}

void* sum(void* args) {
	myargs* my_args = (myargs*) args;
	int start = my_args->start;
	int end = my_args->end;
	int i = 0;
	int s = 0;
	for(i=start;i<end;i++) {
		s += arr[i];
	}
	my_args->sum = s;
	return NULL;
}

void* init_arr(int* arr) {
	int i=0;
	for(i=0;i<NUM;i++) {
		//arr[i] = rand()%100;
		arr[i] = i+1;
	}
	return NULL;
}


// 计算一个大数组的和
int main() {
	init_arr(arr);
	pthread_t td1,td2;

	myargs arg1 = {0,NUM/2,0};
	myargs arg2 = {NUM/2,NUM,0};

	pthread_create(&td1,NULL,sum,&arg1);
	pthread_create(&td2,NULL,sum,&arg2);
	
	pthread_join(td1,NULL);
	pthread_join(td2,NULL);
	printf("sum = %d + %d = %d",arg1.sum,arg2.sum,arg1.sum+arg2.sum);
}
```


### mutex锁的使用

多个线程之间可能会对同一内存空间进行修改，这样就会出现race condition.<br/>
pthread_mutex_lock方法可以对代码快进行加锁


```c
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

#define NUM  100000

pthread_mutex_t lock;

int s = 0;

void* add(void* args) {
	for(int i=0;i<NUM;i++) {
		pthread_mutex_lock(&lock); // 如果不加锁，结果很难预测
		s++;
		pthread_mutex_unlock(&lock); // 释放锁
	}
	return NULL;
}

int main() {
	pthread_t th,th1;
	pthread_create(&th,NULL,add,NULL);
	pthread_create(&th1,NULL,add,NULL);
	pthread_join(th,NULL);
	pthread_join(th1,NULL);
	printf("s=%d",s);
	return 0;
}
```

### false shareing

当多个线程对内存中存储位置很接近的变量进行修改时，容易导致false sharing<br/>
比如thread1修改arr[0]d的值，thread2修改arr[1]的值。
