---
layout: post
title: "Dependence and Data Provider in PHPUnit" 
description: "Dependence and Data Provider in PHPUnit"
---

### 牛人的话(翻译后)

Adrian Kuhn et.al:
	
	写单元测试是一个非常好的做法，帮助开发定位和修复bug，重构代码并作为测试软件的一个单元的文档。
	为了能达到这个效果，单元测试要考虑到程序中所有路径。一个方法或函数通常只覆盖一个特殊的路径。
	然而，一个测试方法并不是一个封闭的，独立的实体。通常在测试方法之间的隐式依赖，就藏在某个测试场景的实现中。


### 
