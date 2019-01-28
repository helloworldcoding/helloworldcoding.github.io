---
layout: post
title: "Vimscript begin"
tags: [vimscript]
description: "vimscript note 2"
---


既然把vimscript是一个脚本语言，那就会有脚本语言的一些共性，比如变量的定义,变量的类型，变量的作用域，分支循环结构,函数等等。

变量
=== 

#### 变量的定义
	
* 一般的变量定义

	- 变量名规范: 字母、数字、下划线，并且不能以数字开头

		:let foo = "bar"
		:let num = 32
		:echo foo num " 显示foo和num
		:unlet foo " 销毁一个变量
		以上好像看出vimscript是动态类型，其实,你们想错了。(党国的高官，怎么会有钱？)

* 配置选项也是变量
	
		比如，我要看配置选项textwidth的值
		:echo &textwidth "需要在前面加上& ,这就告诉vim，你在引用这个选项;如果这个选项不存在，就会报错
		:set textwidth=80
		:echo textwidth "直接输出，也会报错
			E121: Undefined variable: textwidth
			E15: Invalid expression: textwidth
		:echo &textwidth "输出 80

		:set nowrap
		:echo &wrap " 0
		:set wrap
		:echo &wrap " 1
		在vimscript中0为false，非0值整数为true 

		可以用let来设置选项的值，并且let比set更灵活	
		:let &textwidth = &textwidth + 10
		:echo &textwidth " 输出 90
		:set &textwidth = &textwidth + 10 "set 只能给设置一个常量值
			E518: Unknown option: &textwidth = 

		本地选项，将某个选项作为变量来来设置它的本地值，而不是全局值,
		本地变量的前缀:  <code>l:</code>
		:let &l:number = 0 "只对当前buffer生效,当前buffer，行号消失

* 寄存器变量

		:let @a="hello!"
		:echo @a "输出 hello!
		:echo @" "输出无名寄存器的值
		:echo @/ "输出，搜索寄存器的值

* b:变量前缀(只在当前buffer生效的变量)
	
		:let b:hello = "world"
		:echo b:hello " 输出world
		切换到另一个buffer
		:echo b:hello 
			E121: Undefined variable: b:hello
			E15: Invalid expression: b:hello

* help internal-variables

|变量前缀|名称|说明
|:--|:--|:--
|b:|buffer-variable|    	  Local to the current buffer.
|w:|window-variable|    	  Local to the current window.
|t:|tabpage-variable|   	  Local to the current tab page.
|g:|global-variable|    	  Global.
|l:|local-variable|     	  Local to a function.
|s:|script-variable|    	  Local to a \|:source\|'ed Vim script.
|a:|function-argument|  	  Function argument (only inside a function).
|v:|vim-variable|       	  Global, predefined by Vim.
	
分支结构
===
### 条件语句

* 多行命令，写成一行。用管道符(\|)来隔开命令
		
		:echom "foo" | echom "bar"
		:if 1 | :echom "one" | :else | :echom "not one" | :endif " 输出 one
		:if 0 | echom "one" | else | echom "not one" | endif " 输出not one

* if的用法 


	- 必要情况下(数学计算、条件表达式)，vim强制转换变量和字面量的类型。10+"20foo" 最后等于10+20 = 30
	- 以数字开头的字符串会转化为数字，否则会转化为0

			:echo 0+"12.64ab" "输出12
			:echo 0+"-12.34ab" "输出-12

	- if判断时，非零整数为true，0为false

			let a = 3
			if a == 1
				echom "one"
			elseif  a == 2
				echom "two"
			else
				echom "not one or two"
			endif
			"最后输出 not one or two

			if "string"  "等价于 if 0
				echom "zero"
			endif

			if "12Ab" ==# "12ab"
				echom "yeshi"
			else
				echom "no"  " 输出 no, 说明 ==# 没有进行强制转化
			endif

|比较运算符|说明
|:--|:--
|>| 大于,如 10 >1
|<| 小于,如 1 < 10
|==|相等，如 10 == 11 ,"foo" == "bar",会受用户设置noignorecase/ignorecase影响
|==?|字符串比较，大小写不敏感(无论vim对大小写的设置是否敏感),"foo" ==? "FOO" is true
|==#|字符串比较，大小写敏感(无论vim对大小写的设置是否敏感),"foo" ==# "FOO" is false
|>#|大于， 区分大小写的比较
|<#|小于， 区分大小写的比较
|>?|大于， 不区分大小写的比较
|<?|小于， 不区分大小写的比较


所以最好使用==#来比较字符串,统一自己的规范

循环
===

#### for循环

* help for

		let c = 0
		for i in [1,2,3,4]
			let c += i
		endfor
		echom c

		for [lnum, col] in [[1, 3], [2, 5], [3, 8]]
		   echo getline(lnum)[col]
		endfor

#### while循环
		
* help while

		let c = 1
		let total = 0 
		while c <= 4
			let total += c
			let c += 1
		ednwhile
函数
====

#### 没有作用域限制的vimscript函数，必须以一个大写字母开头

```vimscript
function Hello()
	echom "helloworld"
endfunction
call Hello()

function SayHi(name)
	if a:name ==# "Mark"
		return "Hi,Mark"
	endif
endfunction
echom SayHi() " 函数如果没有返回，默认是return 0

"可变参数
function Multiargs(...)
	echom a:0 "参数数量,可变参数的数量
	echom a:1 "第一个参数
	echom a:2 "第二个参数
endfunction
"可变参数和普通参数一起使用
function Multiargs2(foo,...)
	echom a:0 
	echom a:foo
	echom a:1
	echom a:2
	for s in a:000 "a:000,所有的可变参数
		echom ' '.s
	endfor
endfunction
call Multiargs2("hh","jak","luc")
输出：
2
hh
jak
luc
 jak
 luc
可见a:0,只是代表可变参数的数量

"函数参数是只读的，不能在函数内部改变
function Assign(foo)
	let a:foo = "Nope"  
endfunction
call Assign() 会报错，如下：
E46: Cannot change read-only variable "a:foo"

"function! 如果出现重复函数名，感叹号不会报错。
When a function by this name already exists and [!] is
not used an error message is given. 
function! Assign()
	return ["suceess",1]
endfunction

let [msg,code] = Assign() "返回一个数组
```

数据类型
====

#### 布尔

* 0为false， 其他整数为true

#### 数字

* 整数(Number,32位带符号整数(如果编译的时候+num64，就支持64位))
		
		echom 100
		echom 0xff 0X1f  " 0x或0X前缀的来指定16进制
		echom 010 017 "0前缀，是八进制,8,15
		echom 019 "因为它不是一个八进制，所以输出19
		echom 0b1010 "0b前缀，是二进制 ,输出十进制10
		"尽量避免使用八进制
		
* 浮点(Float)
		
		[-+]{N}.{M}
		[-+]{N}.{M}[eE][-+]{exp}
		N,M都是正整数，exp代表10的多少次方,exp也是一个正整数

		echo 12.0 "12.0
		echo 5.45e+3 " 5450.0
		echo 5.3453e9 " 5.3453e9
		if 5.345e2 > 500  " this is true
			echom "biger"
		endif

* 强制转换
	
		echo 3/2 "1
		echo 3.0/2 "1.5 Number和Float混用时，会把Number转换为Float
		echo 1/3.0 "0.33333,默认是6位小数的精度
		echo printf('%.15e',11/7.0)  "1.571428571428571e+00
		echo printf('%.15e',atan(1)) "7.853981633974483e-01

#### 字符串

用双引号或者单引号包围的字符。双引号会转义\n,\\,\"等特殊字符，而单引号不会(除了''转义为'之外）。

- 连接(concatenation)用点号. 和php一样

		echom "hello "."world" "helloworld"
		echom 12."foo" "12foo
		echom 12."123" "12123
		echom 12.1. "foo" "121foo
		echom 12.1."foo" "121foo
		浮点数连接字符串，报错：
		echom 12.1 ."foo" "E806: using Float as a String E15: Invalid expression: 12.1 ."foo"
		echom 12.1 . "foo" "E806: using Float as a String E15: Invalid expression: 12.1 ."foo"

- 特殊字符(双引号才能转义)
		
		help expr-quote
		echom "foo \"bar\""   "输出 foo "bar"
		echom "a\nb" "输出 a^@b
		echo "a\nb" "输出两行，a 和 b

- 字符串字面量(除了''会转义为',其他的原样输出)
		
		help literal-string
		echom '\n\\' "输出\n\\
		echo '12''23' "输出12'34
		echo '12''''23' "输出12''34

- 字符串函数
			
		字符串字节数量
		echom strlen("foo") "3	
		echom strlen("我的1") "7
		echom len("foo") "3

		字符串字符个数
		echom strchars("hello") "5
		echom strchars("我的1") "3

		字符串截取
		strcharpart({src}, {start} [, {len}])
		echo strcharpart("我的123",0,2) "我的
		echo strcharpart("我的123",-1,2) "我,start为负，就是从0开始，截取start+len个字符
		echo strcharpart("我的123",-1,3) "我的
		echo strcharpart("我的123",-1,4) "我的1

		echo "abcdef"[1:3] " bcd
		echo "abcdef"[1:1] " b
		echo "abcdef"[1:] " bcdef
		echo "我的123"[3,6] "的1

		
		分割,默认通过空格分割
		split({expr} [, {pattern} [, {keepempty}]])			*split()*

		echo split("one two three") " ["one","two","three"] 不能用echom输出一个list
		echo split("one,two,three,",") " ["one","two","three"] 不能用echom输出一个list,最后一个,不会有用

		连接,默认通过空格连接
		echo join(["hello","world"]) "  hello world
		echo join(["hello","world"],",") " hello,world
		echo join(["hello","world"],"") " helloworld

		大小写转换
		echom tolower("FOo")
		echom toupper("hello")
		
		字符串查找
		stridx({haystack}, {needle} [, {start}])
		:echo stridx("An Example", "Example")	     3
		:echo stridx("Starting point", "Start")    0
		:echo stridx("Starting point", "start")   -1

		string()强制转化为字符串
			

#### 列表

	help List		
	列表是有序的，元素的集合, 每一个item可以是任何type	
	echo ['foo',34,12.2]	
	echo ['foo',[3,'bar']]

	索引,从0开始
	echo [1,23,[1,34]][0] " 1
	echo [1,23,[1,34]][2][1] " 34
	echo [1,23,[1,34]][-1] "[1,34]

	切割,和python相似，但不同
	echo [1,3,4,5][0:2] "[1,3,4]
	echo [1,3,4,5][0:20000] "[1,3,4,5] 超过列表索引也是安全的。
	echo [1,3,5,6][-2:-1] " [5,6]
	echo [1,2,3,5][:1] "[1,2]
	echo [1,2,3,5][2:] "[3,5]
	字符串也可以切割

	连接+
	echo ['a','b'] + ['c']  " ['a','b','c']

	函数列表
	1.add
	let foo = ['a']
	call add(foo,'b') " 往foo后面加上'b'
	echo foo " ['a','b']
	
	2.len
	echo len(foo) " 2

	3.get,返回指定索引的值
	echo get(foo,0,'default') " a
	echo get(foo,10,'default') " default

	4.index
	echo index(foo,'b') "1
	echo index(foo,'cet') " -1  不存在返回-1

	5.join

	6.reverse ,转置
	echo reverse(['a','b']) "['b','a']

	7.List unpack
	let [var1, var2; rest] = mylist "等同于下面三个表达式
	let var1 = mylist[0]
	let var2 = mylist[1]
	let rest = mylist[2:]

	8.复制和深度复制
	let aa = [1,2,4]
	let bb = aa "aa 和bb 指向同一个列表，改变其中一个，也对相应改变另一个

	let aa = [[1,'a'],3,4]
	let bb = copy(aa) " 浅复制，改变已经有的元素(该元素也是一个列表），aa和bb都会变化，但是，往aa中追加元素，不会影响
	let aa[0][1] = 'aaa' " 浅复制，所以 bb[0][1] == 'aaa'

	如果要做到完全独立，就用deepcopy()
	let cc = deepcopy(aa) " 这样，cc和aa就是完全独立的变量,不会相会影响了。

	9.列表相等
	echo [1,3] == [1,3] " 1 ,true 
	echo [3,1] == [1,3] " 0 ,false
	echo 2 == "2" " 1,true 
	echo [2] == ["2"] " 0, false

	let a = [1,3]
	let b = [1,3]
	echo a is b " 0

#### 字典
	
	类似python中的dict，ruby中的hash, javascript中的object
	字典用花括号创建,索引是字符串（即便定义的时候是数字，也会被转化为字符串)

	:echo {'a':1,'100':'foo',} " 字典最后可以留下一个逗号,js的愚蠢之处，在这里被修复了
	
	let dic = {'a':1,'100':'foo',}
	echo dic['a'] " 1
	
	当键是字母数字下划线组成时，字典也支持"点"查找
	echo dic.a
	echo dic.100

	赋值和添加，移除项
	let dic.a = 222
	let dic.c = 'new val'
	
	let a = remove(dic,'a') " 此方法移除a后，会返回a对应的值
	unlet dic.a "直接移除a，不会返回

	字典函数
	echo get(dic,'a','default') " 1 和list一样
	echo get(dic,'dd','not') " not

	echo has_key(dic, "a") " 1

	echo items(dic) " [['a',1],['100','foo']]
	echo  keys(dic) "['a','100']
	echo values(dic) "[1,'foo']

	遍历Dictionary
	for key in keys(dic)
		echo key.":'.dic[key]
	endfor

	for v in values(dic)
		echo "value:".v
	endfor
	
	for [k,v] in items(dic)
		echo k.':'.v
	endfor

	合并数组
	call extend(adic, bdic) "把bdic的值，加到a中，如果有重复的key，a的会被b覆盖(默认)

	过滤元素
	call filter(dict, 'v:val =~ "x"') " 把value匹配"x"的元素都剔除掉。

	
#### 函数(Funcref)

#### 特殊(Special)

	v:false
	v:true
	v:none
	v:null

#### 任务(Job)

	job_start()

#### 通道(channel)

	ch_open()

对大部分命令和选项，vim支持他们的缩写。但是建议在vimrc或者自己编写的插件中使用全称，这样才会有更好的可读性。
 
