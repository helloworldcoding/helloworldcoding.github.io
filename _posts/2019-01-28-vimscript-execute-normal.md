---
layout: post
title: "Execute & normal in Vimscript"
tags: [execute, normal, vimscript,vim]
description: "command execute and noraml in vimscript"
---

Execute命令
====

execute命令，把字符串当作vimscript命令来执行.

#### 基本用法

:exe[cute] {expr1} ..	
	把{expr1}当作一个Ex命令来执行，并返回结果。多个参数，用空格分隔。

	多个命令可以用| 连起来
	:execute '!ls' | echom "theend" 

	在execute后面的字符串中可以使用\<esc>这样的方式来使用\e,看上去跟明显
	\e	escape <Esc>
	\f	formfeed <FF>
	\n	newline <NL>
	\r	return <CR>
	\t	tab <Tab>

	当文件名中有特殊字符的时候，要小心了。
	:execute "e ".fnameescape(filename) " fnameescape()函数用在vim的命令中
	:execute "!ls " . shellescape(filename, 1) " shellescape()函数用在shell命令中

	当要执行多行的命令时，用|来 执行得了,|要放在引号之内
	:execute 'while i < 5 | echo i | let i = i + 1 | endwhile'

	注释,|"之后的是注释
	execute "echo 'foo'" | " this is a comment


Normal命令
===

normal 命令简单地接受一串键值并当作是在normal模式下输入的

	:normal ggdd  "删除第一行
	:normal G "一般情况下，这个会把光标移到最后一行。
	"但是如果用户设置映射呢?比如nnoremap G dd ,这个时候，就会变成删除了。。。。惊不惊喜？
	:normal! G "这样就能确保光标移到最后，即便用户设置了G的映射，映射也不会生效。

	所以，在写vim脚本的时候，应该总是用normal!,因为你不确定用户在他的.vimrc文件中做了哪些映射

	:normal! /foo<cr> "这样不会起作用 ,这种情况，需要结合execute来进行

	:execute "normal! /foo\<cr>" " 这样就可以起到查找foo的作用了
	
	:execute "normal! mqA;\<esc>`q" "在行尾添加一个;然后光标回到原来的位置

#### 转义字符
```
"string"		string constant		*expr-quote*

Note that double quotes are used.

A string constant accepts these special characters:
\...	three-digit octal number (e.g., "\316")
\..	two-digit octal number (must be followed by non-digit)
\.	one-digit octal number (must be followed by non-digit)
\x..	byte specified with two hex numbers (e.g., "\x1f")
\x.	byte specified with one hex number (must be followed by non-hex char)
\X..	same as \x..
\X.	same as \x.
\u....	character specified with up to 4 hex numbers, stored according to the
	current value of 'encoding' (e.g., "\u02a4")
\U....	same as \u but allows up to 8 hex numbers.
\b	backspace <BS>
\e	escape <Esc>
\f	formfeed <FF>
\n	newline <NL>
\r	return <CR>
\t	tab <Tab>
\\	backslash
\"	double quote
\<xxx>	Special key named "xxx".  e.g. "\<C-W>" for CTRL-W.  This is for use
	in mappings, the 0x80 byte is escaped.
	To use the double quote character it must be escaped: "<M-\">".
	Don't use <Char-xxxx> to get a utf-8 character, use \uxxxx as
	mentioned above.

Note that "\xff" is stored as the byte 255, which may be invalid in some
encodings.  Use "\u00ff" to store character 255 according to the current value
of 'encoding'.

Note that "\000" and "\x00" force the end of the string.
```

