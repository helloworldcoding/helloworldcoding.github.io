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
	var variable_name [SIZE1][SIZE2]...[SIZEN] variable_type  //多维数组
	var thread [5][10][2] int // 定义一个三维数组

- 数组初始化
	var balance = [5]int{1,3,3,4} // 初始化一个数组，长度为5,元素是1,3,3,4
	var balance = [...]int{1,3,3,4} // 初始化一个数组，可以不指定数组大小
	var balance = [2][2]int{
		{1,3},
		{3,5},  // 这行尾里要有逗号
	}

- 访问数组元素	
	通过索引访问,索引从0开始
	var salary int = balance[1] // 获取第二个元素 3
```
	var arr = [5]int{1, 3, 4, 5}
	// 遍历数组1
	for i := 0; i < 5; i++ {
		fmt.Printf("arr[%d]= %d\n", i, arr[i])
	}
	// 遍历数组2
	for k, v := range arr {
		fmt.Printf("arr[%d]= %d\n", k, v)
	}	
```

- 向函数传递数组
	
	void myFunc(arr [10]int) // 指定了数组的大小,该函数只能接受指定长度的数组
	{
		...
	}

	void myFunc1(arr []int) // 不指定数组的大小,该函数只能接受不定长度的数组
	{
		...
	}


#### 指针

> 取地址符&,返回一个变量的内存地址

	var a int = 10
	fmt.Printf("a的内存地址:%x\n",&a)  // 输出十六进制 c00006e060

- **指针变量**
	
	变量的内存地址(一个整数而已)，也可以保存在其他变量中，这种变量就是`指针变量`
	虽然它存储的其实就是一个整数，但是定义的格式不能按照整型变量来。

	定义指针变量
	var var_name *var_type

	var ip *int // 这个指针变量ip，只能用它来存放一个整型变量的地址
	var fp *float32 // fp可以存放一个float32类型变量的地址

	指针变量赋值
	var a int = 10
	var ptr *int
	ptr = &a //ip的值就是变量a的内存地址

	获取指针变量指向的内容
	*ptr //就可以取出变量a的值，及10

	空指针（申明了，但是不赋值的指针变量,它的值就不是大于零的整数(内存地址)，而是nil)
	if ptr != nil // 不是空指针
	if ptr == nil // 空指针 ,fmt.Printf("%x\n",ptr),输出是0

	指针数组
	const MAX int = 3
	var ptr [MAX]*int //定义一个长度为3的数组，数组的元素是指向整型的指针

	指针变量作为函数的参数
	func swap(a *int ,b *int) { // a和b都是指针变量
		*a,*b = *b ,*a
	}
	
	指针的指针
	var a int = 12
	var p *int
	var ptr **int
	p = &a
	ptr = &p //ptr的值是一个内存地址，这个地址指向变量p,而变量p又是一个指向整数的指针，

#### 结构体

- 定义:
	// 定义后的结构体就可以当作类型，用来申明变量了。
	type struct_var_type struct {
		member definition;
		member definition;
		...
		member definition;
	}

	type Book struct {
		title string
		author string
	}

- 定义结构体类型的变量
	var_name := struct_type {v1,v2,...,vn}
	var_name := struct_type {k:v1,k2:v2,...,kn:vn}

	1.
		var cbook Book
		cbook = Book{"c","ken"}
	2. 
		gobook := Book{"golang","poke"}

- 访问结构体成员:
	struct_val.member

- 结构体指针
	var struct_pointer *Books //定义一个指向Books结构体变量的指针
	struct_pointer = &Book1;
	结构体指针可以访问结构体成员
	struct_pointer.title; // 等同与 Book1.title


#### Slice切片

> slice是对数组的抽象。数组的长度固定,有时不发方便使用。切片的长度不固定，可以追加元素，容量增大

- 定义和初始化
	
	var identifier []type //声明一个切片,没有指定大小,此时值为nil
	var identifier []type = make([]type,len) // 声明一个切片，指定大小
	identifier := make([]type,len) // 同上
	identifier := make([]type,len,capacity) // 声明一个切片，长度len，容量capacity

	//直接初始化
	identifier := []int{1,3,5} // 初始化一个切片，[]int表示切片的类型，{1,2,3}是初始化的值

	//用数组初始化切片
	identifier :=arr[start:end] // 用数组从下标start到end-1的元素创建一个slice

	s1 := arr[start:] // 从下标start到最后
	s1 := arr[:end] // 下标从0开始到end-1的元素
	s1 := arr[:] // 所有的元素

	var s2 = make([]int,3,5)  // s2的值，{0,0,0}
	var s3 []int //  s3 == nil

	len切片<=cap切片<=len数组

- 切片截取
	和数组截取一样
	s4 := s2[1,3] // {0,0}
	
- 切片append()&copy()
	s := []int{1,3,4,5}
	s = append(s,6) // s变为1,3,4,5,6
	s = append(s,7,8) // s变为1,3,4,5,6,7,8

	s2 := make([]int,len(s),cap(s)*2)
	copy(s2,s) // 把s的内容复制到s1

#### Range

> `range`关键字用于for循环中 迭代数组，切片，通道或集合或字符串的元素。在array和slice中返回元素的索引和对应的值

	for i,v := range arr{
		...
	}
	
	for _,v := range slie {
		...
	}

	for k :=range map {
		Println("key=",k,"value=",map[k])
	}

#### map

- 定义Map
	var identifier map[key_type]val_type // var my_map map[string]int
	map_var = make(map[key_type]val_type) // info = make(map[string]int)
	
	如果不初始化，就是一个nil map

	
	my_map["name"] = "shangsna"
	my_map := map[string]string{"name":"lisi","hobby":"reading"} 

	name,err = my_map["name"]
	if err == nil {
		存在该key
	}else {
		不存在该key
	}

#### Interface

> Interface(接口),把所有共性的方法定义在一起，其他类型只要实现了这些方法就是实现了这个接口

	type interface_name interface {
		method1 [return_type] // 返回值可以没有
		method2 (int,int)
		...
		methodn string
	}
		
	定义结构体
	type struct_name struct {
		// variables
	}

	用这个结构体去实现这个接口
	func (st1 struct_name) method1()[return_type] {
		//实现这个方法
		...
	}
	...
	func (stn struct_name) methodn()[return_type] {
		//实现这个方法
		...
	}

	/*定义一个Phone接口*/
	type Phone interface {
		call() int  //返回值是整型
		camera(int) (int, string) // 返回值有两个
	}

	/**定义一个Huawei结构体**/
	type Huawei struct {
		name string
	}

	/* 用Huawei结构体，实现了一个call()方法
	func (matex Huawei) call() int {
		fmt.Println("matex belint the world")
		return 0
	}

	/* 实现一个camera()方法 */
	func (matex Huawei) camera(px int) (int, string) {
		fmt.Println("take great pic",px ,"px")
		return 0, "huawei"
	}
