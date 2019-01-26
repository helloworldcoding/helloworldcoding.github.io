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

* 多行命令，写成一行。用管道符(|)来隔开命令
		
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
```

数据类型
====

#### 数字

* 整数(Number,32位带符号整数)
		
* 浮点
对大部分命令和选项，vim支持他们的缩写。但是建议在vimrc或者自己编写的插件中使用全称，这样才会有更好的可读性。
 
