---
layout: post
title: "My tour of golang" 
tags: [golang]
description: "fundentmentals of golang" 
---


#### Basic types

- 布尔(bool)   默认值false
- 字符串(string) 默认值""
- 数字类型
	- int  int8 int16 int32 int64  有符号整数 默认值0 int(-128,127)
	- uint uint8 uint16 uint32 uint64 uintptr  无符号整数 uint(0,255)
	- byte // int8的别名
	- rune // int32的别名,代表unicode值
	- float32 float64 默认值0 浮点数
	- complex64(32位实数和32位虚数) complex128(64位实数和64位虚数)
- 派生类型
	- 指针类型(pointer)  默认值是nil
	- 数组类型
	- 结构化类型(struct)
	- channel类型
	- 函数类型
	- 切片类型
	- 接口类型(interface)
	- Map类型


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

#### Constants常量

1.定义格式
> const Varname [type] = value

2.其他说明
> 1. 常量的值可以是内置函数的返回值,`const LEN int = len("asdf")`
> 2. 多个常量(常量组)
```
const (
		a = "abc"
		b = len(a)
		c = b + b*b
		d     // 常量组，如果不提供初始化值，则表示用上一行的表达式的值,所以d 为12
		e  // 同上e 为12
	  )
```
	

#### 运算符
	
	1. 关系运算符: == != > < >= <= 
	2. 逻辑运算符: &&  ||  ! 
	3. 位运算(都是双目): & | ^(按位异或) << >>
	4. 赋值运算符: 
		 = += -= *= /= %= <<= >>=
		 &= ^= |=( c|=2等于 c = c|1）

	5. 指针相关的运算符(单目): &(取地址符) *(获取指针的值)
		var ptr *int // 定义一个指向整型的指针变量
		a := 123
		ptr = &a // 获取变量a的地址,赋值给指针变量ptr
		val = *ptr // 获取指针ptr指向的值

#### 条件语句
	
```golang
1. if 
  if a > 1 {
	fmt.Println("big")
  }
2. if else
 if a > 1 {
	fmt.Println("big")
 }else {
	fmt.Println("small")
 }

3. swith case 终于去到了其他编程语言的break;
 switch variable {  
	 case val1:
		 Println("var1")
	 case val2:
		 Println("var1")
	 case val4,val5,varl6:  // 符合多个变量
		 Println("var456")
			 ...
	 default:
		Println("default")

 }
a. 变量var可以是任何类型的，而val1和val2必须时var同类型的任意值，或者是最终结果是var同类型的表达式
b. 一般情况下只会执行一个case 

4. select

select是Go中的一个控制结构，类似于用于通信的switch语句。每个case必须是一个通信操作，要么是发送要么是接收。

select随机执行一个可运行的case。如果没有case可运行，它将阻塞，直到有case可运行。
一个默认的子句应该总是可运行的。

 var c1, c2, c3 chan int
   var i1, i2 int
   select {
      case i1 = <-c1:
         fmt.Printf("received ", i1, " from c1\n")
      case c2 <- i2:
         fmt.Printf("sent ", i2, " to c2\n")
      case i3, ok := (<-c3):  // same as: i3, ok := <-c3
         if ok {
            fmt.Printf("received ", i3, " from c3\n")
         } else {
            fmt.Printf("c3 is closed\n")
         }
      default:
         fmt.Printf("no communication\n")
   }    


```
#### 循环结构

```golang

1. for

var b int = 15
   var a int

   numbers := [6]int{1, 2, 3, 5} 

   /* for 循环 */
   for a := 0; a < 10; a++ {
      fmt.Printf("a 的值为: %d\n", a)
	  if a == 7 {
		continue	
	  }
	  if a == 9 {
		break
      }		
   }

   for a < b {
      a++
      fmt.Printf("a 的值为: %d\n", a)
   }
   
	/*
	* label: statement
    * goto label
	*/
   DEBUG1:for i,x:= range numbers {
	  if i == 4 {
		goto DEBUG1   // goto的使用
	  }	
      fmt.Printf("第 %d 位 x 的值 = %d\n", i,x)
   }   

```

#### 函数

格式:
	func function_name([parameter list]) [return_types] {
		...
	}	

#### 变量作用域

- 函数体内部定义的变量(局部变量),只在函数体内部有用
- 函数外部定义的变量(全局变量),可以在整个包甚至外部包(被导出后)使用
- 函数定义中的变量(形式参数,也是局部变量)
- 当局部变量与全局变量的名称相同时，局部变量优先使用

```golang
func main() {
	var a int = 0
	Println(a) // 0 
	// 在for循环的initialize中(a:=0),此时for循环的a 和局部变量a不是同一个变量。initialize的a是for循环的局部变量，for循环结束后，就不能访问了。
	for a:=0; a < 10; a++ {
		Println(a) // 0,1,2,...,9
	}
	Println(a) // 0
}

```

#### 数组

- 数组申明: `var variable [SIZE] variable_type`
	var arr [10] int
	var variable_name [SIZE1][SIZE2]...[SIZEN] variable_type 
	var thread [5][10][2] int
- 数组初始化
	var balance = [5]int{1,3,3,4}
	var balance = [...]int{1,3,3,4} // 初始化一个数组，可以不指定数组大小，玩够了公式开支
