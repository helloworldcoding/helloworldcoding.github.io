---
layout: post
title: "My tour of golang" 
tags: [golang]
description: "fundentmentals of golang" 
---


#### Basic types

- bool   默认值false
- string 默认值""
- int  int8 int16 int32 int64 默认值0
- uint uint8 uint16 uint32 uint64 uintptr
- byte // int8的别名
- rune // int32的别名,代表unicode值
- float32 float64 默认值0
- complex64 complex128


#### 类型转换

- T(v) 显示类型转换，如int(12.4),string(11244) 
	- 不过整型转为string是按照ascii码或unicode码转为对应的字符，所以有可能得到乱码

|原类型\转化后|bool|int|float|string|complex
|:--|:--|:--|:--|:--|:--
|bool|1 |0 |0  |0  | 0 
|int|0 | 1|1  |1  | 0
|float|0 |1 |1  |0   | 0
|string| 0|0 |0  | 1   | 0
|complex|0 |0 | 0 | 0   | 1

#### Variables with initializers(变量和初始化)

	// 再函数内部以下三种写法都一样
	var i,j int = 1, 3  // 全局可以用
	var i, j = 1,3     // 全局可以用
	i,j := 1,3 // 只能在函数体内部使用
