---
layout: post
title: "Vim command mode--Ex(1)"
description: "something about vim command"
tags: [vim,command model,Ex]
---

最早，现有ed，ed为ex之父，ex为vi之父，而vi为vim之父 ---- The Old Testament of Unix

这些早期的Unix编辑器，依旧可以在vim中找到它们的影子，他们没有消失，只是换了一个方式存在。

我打算挖一坑，来說一下命令行模式，来看一下vim身体里流淌着多少它的祖先ex(行编辑器)的血液。

在普通模式下，按下<code>:</code>就会进入命令行模式，和shell下的命令行很相似;输入一条命令，然后回车执行。按下<code>Esc</code>键就可以回到普通模式。这些在命令行模式下执行的命令，又可以称为Ex命令。

|常见命令|用途说明
|:--|:--
|:edit|编辑一个文件，editk可以简写为e
|:write|写文件
|:tabnew|创建一个tab
|:prev/\:next|当用vim一次打开多个文件(如：vim a.txt b.txt)后，可以用他们来回切换文件
|:bprev/\:bnext|当打开多个缓冲区后，可以用他们来回切换缓冲区，不过我习惯<code>ctrl</code>+6来回切换最近两个缓冲区
|:[range]delete [x] |删除指定范围的行到寄存器[x]中,delete可以简写为d
|:[range]yank [x] |复制指定范围的行到寄存器[x]中,yank可以简写为y
|:[line]put [x] | 把寄存器x的内容，粘贴到指定行的后面,put不能简写
|:[range]copy {address} |把指定范围的行复制到{address}指定的行之下,copy可以简写为co或t
|:[range]move {address} |把指定范围的行移动到{address}指定的行之下,move可简写为m
|:[range]join |把指定范围内的行连接起来，成为一行，<code>J</code>也可以起连接行的作用(当前行和下一行）
|:[range]normal {commands} | 对指定范围内的每一行执行普通模式的命令{commands}
|:[range]substitute/{pattern}/{string}/{flags} | 把指定范围内的行出现的{pattern}匹配的地方替换为{string}，substitute 可以简写为s(vim的正则替换，我先挖个坑，后面再填上)
|:[range]global/{pattern}/[cmd]| 把指定范围内的行匹配{pattern}的所有行之下Ex命令{cmd}，这个特性还要进一步挖掘

range的写法：
	- 一个数字如23,34;代表指定的行
	- 两个数字逗号分割: 2,5  代表从第2到第5行
	- num,.  从第num行到当前行，比如我们要从第7行删除所有内容，我们先按G,光标会移动到最后一行，然后输入 :7,. d


总之，Ex命令可以一次执行，多处修改。
