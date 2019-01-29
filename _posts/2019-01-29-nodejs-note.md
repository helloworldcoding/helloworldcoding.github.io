---
layout: post
title: "My nodejs note"
tags: [nodjs, tiny demo]
description:  "one night teach myself nodejs"
---

Nodejs is just a kind of of js..^\_^

基本语法
====

pass

工程相关
===

### 模块

在模块中，用require()方法来引入外部模块。用exports对象用于导出当前模块的方法或变量,只有导出的变量或方法才能被其他模块使用。就像是oop中的public。这点和erlang有点像，erlang是声明一个导出列表<code>-export([add/1,...])</code>
在模块中，module对象，代表自身，exports也是module的一个属性.
在nodejs中，一个文件就是一个模块.(其他编程语言也差不多)

	require()的参数就是模块标识。参数可以是小驼峰命名的字符串(模块名?),
	也可以是相对路径，或绝对路径。

* 两种模块，Node提供的核心模块(fs,http,path...)，用户编写的称为文件模块。

* 模块加载，优先从缓存加载，核心模块加载，路径形式的模块，自定义模块(加载最慢，一种特殊的文件模块)

	- 自定义模块
		
			文件内容：
			console.log(module.paths);
			node ./test.js,输出如下:
			[ '/home/wm/code/nodejs/one_night/node_modules',
			  '/home/wm/code/nodejs/node_modules',
			  '/home/wm/code/node_modules',
			  '/home/wm/node_modules',
			  '/home/node_modules',
			  '/node_modules' ]
			node查找自定义模块的规则：
			当前文件目录的node_modules目录下查找
			父目录的node_modules目录
			...
			直到根目录下的node_modules目录
	
	- 引用了不同的模块，如果他们有相同的导出方法或属性。后面引用的模块会覆盖先引用的模块
		
			
			./node_modules/demo.js内容:
			hello = function(){
				console.log("hello world");
			}
			exports.hello;

			./node_modules/hi.js内容:
			hello = function(){
				console.log("hi world");
			}
			exports.hello;

			./test.js内容如下:
			require('hi');
			require("demo");
			hello(); // 输出 hello world
			console.log(module.paths);
			
			require("demo");
			require('hi');
			hello(); // 输出 hi world

	- 文件扩展名.当标识符没有扩展名时 Node会按.js .json .node的顺序依次补充扩展名，以此尝试
	
	- 目录分析和包  
			
			在node_modules/目录下的子目录，可以做成一个包，package
			前提是，这个目录下要有一个package.json文件，并且文件能通过JSON.parse()解析出描述对象。
			比如main属性，指定文件名进行定位。
			如果没有package.json文件，node会依次查找index.js index.json index.node

			./test.js内容如下:
			require("mypackage");
			test("guogu0"); // 输出 myname is guoguo

			在当前./node_modules/下发现了子目录./node_modules/mypackage/
			mypackage/目录下有package.json文件，内容如下
			{
				"name":"mypack",
				"version": "0.0.1",
				"main": "./src/bar.js" // 先对package.json文件的路径
			}
			通过main属性可知，这个mypackage包 指定的是 ./node_modules/mypackage/src/bar.js文件
			该bar.js文件内容如下：
			test = function(name) {
				console.log("myname is "+name);
			}
			exports.test = test;

			所以test.js在require("mypackage")的时候，其实是找到了一个目录，也就是一个包。
