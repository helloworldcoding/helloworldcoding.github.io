---
layout: post
title: "Some Vim skills"
description: "Some awosome vim skills"
---

#### 编辑后，发现需要root权限

	:w ! sudo tee %

#### 做计算

	insert模式，ctrl+r =3600*24*7

#### 查看字符ascii码,16进制，8进制

	common模式，ga 查看光标下的字符的ascii码	

#### 插入unicode字符
#### 大小写转换
```
~          将光标下的字母改变大小写
3m~         将光标位置开始的3个字母改变其大小写
g~~        改变当前行字母的大小写
U          将可视模式下选择的字母全改成大写字母
u          将可视模式下选择的字母全改成小写
gUU        将当前行的字母改成大写
3gUU       将从光标开始到下面3行字母改成大写
guu       将当前行的字母全改成小写
gUw       将光标下的单词改成大写。
guw       将光标下的单词改成小写。
```

#### Insert mode 缩进
	
	ctrl+t >	
	crtl+d <

#### !的作用
	
	- 切换布尔选项，:set number!
	- 执行shell命令 :! ls
