---
layout: post
title: "Some note about vimrc"
description: "something about the vimscript --- fundamentals"
tags: [vim,vimscript,statusline]
---



### 启动vim不加载.vimrc

	vim -u NONE -N    -N 就是和vi不兼容
	-u <vimrc>		Use <vimrc> instead of any .vimrc
	-N			Not fully Vi compatible: 'nocompatible'
### 注释 
	
	"  这就是注释,双引号之后的就是注释内容
	但是下面的注释会失效。。
	:map <space> viw " 按下空格键高亮选中一个单词	
	
	所以vim的注释，最好不要行末添加

### !切换布尔选项
	
	:set number!   或:set nu!

### ?查看配置的值

	:set fileencoding? 返回fileencoding=utf-8
	:set number? 返回 number 或 nonumber

### 键盘映射

- normal模式下的简单映射

		:map - dd   按下 - 就相当于dd，会删除一行
		也可以用<keyname>来告诉vim一个特殊的按键
		:map <space> viw  按下空格，vim将高亮选中一个单词
		
		:unmap - 取消-的映射
		:unmap <space>

- nmap 在normal模式下的映射

		:nmap - ddp
		取消映射
		:nunmap -

- vmap 在visual模式下的映射
		
		visual模式下，按下\,会把小写字母变大写
		:vmap \ U 
		取消映射
		:vunmap \

- imap 在insert模式下的映射
		
		在insert模式下，ctrl+d 会删除一行
		:imap <c-d> <esc>ddi   
		取消映射
		:iunmap <c-d>
	
- 映射可能会出现递归调用
		
		:nmap - dd
		:namp \ -
		在normal模式下，按下\,vim会解释为-,而 -会继续被解析为 dd，最后就是删除了一行

- 非递归映射*noremap
	
		:nmap x dd
		:nnoremap \ x
		当按下\ 就是x，删除一个字符，vim不会把x再解析为dd

		** 应该在所有的情况下使用 noremap **
		" insert mode下jj --> <esc>
		inoremap jj <esc>
		noremap/nnoremap , vnoremap, inoremap
		取消非递归映射(和取消递归映射的方法一样)
		:unnmap  \

		:map x <nop> 就会让按键x失效

		:nnoremap <buffer> Q x 只在当前缓冲区生效

### Leaders(导航键?)

	vim默认的leader是\

	:let mapleader = "-"  把leader键设置为-
	在配置文件的其他地方，就用<leader>,而不是"-"

	local leader 这个leader只用于那些对某类文件(python,html)而设置的映射
	
	:let maplocalleader = "\\"  把loacalleader设置为\,以为\\会转意为\
	如果要设定一个只会用户特定缓冲区的映射，一般会使用 <localleader>,而不是<leader>
	使用<leader>和<localleader>按键就像设置了一种命名空间,
	用<localleader>来设置本地映射，会防止你的插件覆盖别人的全局映射
	
	打开vimrc文件
	nnoremap <leader>ev :vsplit $MYVIMRC<cr>
	重新加载vimrc文件
	nnoremap <leader>sv :source $MYVIMRC<cr>

### Abbreviations

在一个abbrevivation后输入一个 non-keyword character后，vim会替换那个abbrevivation
not-keyword character指的是那些不在iskeyword选项中的字符(iskeyword=@,48-57,_,192-255)



- insert模式下的abbrevivations
		
		:iabbrev adn and
		:iabbrev ture true

- replace模式下的abbrevivations

- command模式下的abbrevivations



### 自动命令
	
	:autocmd BufNewFile * :write  "该命令会在打开一个文件后，自动自动保存
	         ^          ^ ^
	         |          | |
	         |          | 要执行的命令
	         |          | 
	         |          用于事件过滤的"模式pattern" 
	         |           
	         要监听的事件          
	
	:autocmd BufNewFile *.php :write  只会保存php文件
	:autocmd BufWritePre *.html :normal gg=G  在保存html文档之前，先格式化
	多个事件用逗号分隔
	:autocmd BufNewFile,BufRead *.html setlocal nowarp
	vim脚本编程中，一般会同时使用BufRead和BufNewFile


- FileType 事件
	
	针对不同的文件，设置一些有用的映射,比如注释
	可以设置localleader为,,
	:let maplocalleader=",,"
	:autocmd FileType erlang nnoremap <buffer> <localleader>c I%%<esc>
	然后打开一个erlang文件，normal模式下，按下,,c 就可以注释一行
	
- 本地缓冲区缩写
	
	:iabbrev <buffer> --- test  对当前缓冲区生效，会覆盖如下的全局设置
	:iabbrev  --- tes2   会话的所有缓冲区都生效，但是优先级没有<buffer>定义的缩写高

	:autocmd FileType php :iabbrev <buffer> iff if () {<cr>}<cr><esc>kkA<left><left><left>
	:sleep 10m 睡眠10ms

### 自动命令组Grouping Autocommands

	:aug[roup] {name}  group name 大小写敏感
		...
	:augroup end/END
	
	:augroup testgroup
	:autocmd BufWrite * : echom "foo"
	:autocmd BufWrite * : echom "bar"
	:augroup END

	定义两个名称相同的命令组，一般情况下，后面定义的不会覆盖前面的,除非把autocmd!放在组里
	:augroup testgroup
	:autocmd!
	:autocmd BufWrite * : echom "test"
	:augroup END

	更多详情： :help autocmd-groups


### Operator-Pending映射

	:help omap-info 	

	Operator-pending mappings can be used to define a movement command that can be
	used with any operator.  Simple example: ":omap { w" makes "y{" work like "yw"
	and "d{" like "dw".

	:onoremap { :<c-u>normal! 0f{vi{<cr>   "在包含{}的一行，执行d{、c{、y{  就可以删除/改变/复制{}之间的内容
	:onoremap ( :<c-u>normal! 0f(vi(<cr>  "在包含()的一行，执行d(、c(、y(  就可以删除/改变/复制()之间的内容 

	The CTRL-U (<C-U>) is used to remove the range that Vim may insert. 


	:onoremap ih :<c-u>execute "normal! ?^==\\+$\r:nohlsearch\rkvg_"<cr>

	normal! 可以执行一些命令，但是遇到特殊字符就不行了，比如<cr>
	execute 把后面的字符串当作命令来执行。特殊字符需要转义,如\r代表回车 \\代表\

	g_: 移动到当前行的最后一个非空字符。$会选中最后一个换行符

### 状态条statusline
	
statusline可以展示一些当前buffer内文件的信息，比如文件名称%f,完整路径%F, %m如果缓冲区内容修改表示为[+]
默认情况下，statusline是隐藏的。显示： <code>:set laststatus=2</code>,隐藏 <code>:set laststatus=0</code>

|定制项目|参数意义
|:--|:--
|%f|文件名称
|%F|文件完整路径,我觉得这个完整路径有助于回答,我在哪里?
|%m|如果缓冲区内容发生改变表示为[+],有助于你提醒自己，你在做什么？
|%n|缓冲区号,
|%y|文件类型，不过我觉得如果输出了文件名，这个有点多余
|%v|虚列号	
|%l|行号
|%L|总行数
|%=|之后的状态都是右对齐,在这个之前是左对齐
|%{expr}|表达式的结果，可以用这个定制很多特性的状态

我的statusline设置
	
	set statusline=buf%n\ %m
	set statusline+=\ %l,%v/%L
	set statusline+=\ %{&fileencoding?&fileencoding:&encoding} "文件编码
	set statusline+=\ [%{&fileformat}] "文件类型
	set statusline+=\ %{(exists(\"bomb\")\ &&\ &bomb)?\"Bom\":\"\"} "如果有bomb头，就会显示Bom
	set statusline+=%{StatuslineGit()} "显示git分支名称,需要定义如下两个函数
	set statusline+=\ %F

	function! GitBranch()
	  return system("git rev-parse --abbrev-ref HEAD 2>/dev/null | tr -d '\n'")
	endfunction

	function! StatuslineGit()
	  let l:branchname = GitBranch()
	  return strlen(l:branchname) > 0?'  '.l:branchname.' ':''
	endfunction


	\<space> 是空格
	最后的效果如下：
	buf1  223,5/365 utf-8 [unix]   master  ~/Documents/github.io/_posts/2019-01-23-vimscript-note-1.md
	


### autocmd 所有的事件

* :help autocmd-events

```
Name			triggered by ~

	Reading
|BufNewFile|		starting to edit a file that doesn't exist
|BufReadPre|		starting to edit a new buffer, before reading the file
|BufRead|		starting to edit a new buffer, after reading the file
|BufReadPost|		starting to edit a new buffer, after reading the file
|BufReadCmd|		before starting to edit a new buffer |Cmd-event|

|FileReadPre|		before reading a file with a ":read" command
|FileReadPost|		after reading a file with a ":read" command
|FileReadCmd|		before reading a file with a ":read" command |Cmd-event|

|FilterReadPre|		before reading a file from a filter command
|FilterReadPost|	after reading a file from a filter command

|StdinReadPre|		before reading from stdin into the buffer
|StdinReadPost|		After reading from the stdin into the buffer

	Writing
|BufWrite|		starting to write the whole buffer to a file
|BufWritePre|		starting to write the whole buffer to a file
|BufWritePost|		after writing the whole buffer to a file
|BufWriteCmd|		before writing the whole buffer to a file |Cmd-event|

|FileWritePre|		starting to write part of a buffer to a file
|FileWritePost|		after writing part of a buffer to a file
|FileWriteCmd|		before writing part of a buffer to a file |Cmd-event|

|FileAppendPre|		starting to append to a file
|FileAppendPost|	after appending to a file
|FileAppendCmd|		before appending to a file |Cmd-event|

|FilterWritePre|	starting to write a file for a filter command or diff
|FilterWritePost|	after writing a file for a filter command or diff

	Buffers
|BufAdd|		just after adding a buffer to the buffer list
|BufCreate|		just after adding a buffer to the buffer list
|BufDelete|		before deleting a buffer from the buffer list
|BufWipeout|		before completely deleting a buffer

|BufFilePre|		before changing the name of the current buffer
|BufFilePost|		after changing the name of the current buffer

|BufEnter|		after entering a buffer
|BufLeave|		before leaving to another buffer
|BufWinEnter|		after a buffer is displayed in a window
|BufWinLeave|		before a buffer is removed from a window

|BufUnload|		before unloading a buffer
|BufHidden|		just after a buffer has become hidden
|BufNew|		just after creating a new buffer

|SwapExists|		detected an existing swap file

	Options
|FileType|		when the 'filetype' option has been set
|Syntax|		when the 'syntax' option has been set
|EncodingChanged|	after the 'encoding' option has been changed
|TermChanged|		after the value of 'term' has changed
|OptionSet|		after setting any option

	Startup and exit
|VimEnter|		after doing all the startup stuff
|GUIEnter|		after starting the GUI successfully
|GUIFailed|		after starting the GUI failed
|TermResponse|		after the terminal response to |t_RV| is received

|QuitPre|		when using `:quit`, before deciding whether to quit
|VimLeavePre|		before exiting Vim, before writing the viminfo file
|VimLeave|		before exiting Vim, after writing the viminfo file

	Various
|FileChangedShell|	Vim notices that a file changed since editing started
|FileChangedShellPost|	After handling a file changed since editing started
|FileChangedRO|		before making the first change to a read-only file

|ShellCmdPost|		after executing a shell command
|ShellFilterPost|	after filtering with a shell command

|CmdUndefined|		a user command is used but it isn't defined
|FuncUndefined|		a user function is used but it isn't defined
|SpellFileMissing|	a spell file is used but it can't be found
|SourcePre|		before sourcing a Vim script
|SourceCmd|		before sourcing a Vim script |Cmd-event|

|VimResized|		after the Vim window size changed
|FocusGained|		Vim got input focus
|FocusLost|		Vim lost input focus
|CursorHold|		the user doesn't press a key for a while
|CursorHoldI|		the user doesn't press a key for a while in Insert mode
|CursorMoved|		the cursor was moved in Normal mode
|CursorMovedI|		the cursor was moved in Insert mode

|WinNew|		after creating a new window
|TabNew|		after creating a new tab page
|TabClosed|		after closing a tab page
|WinEnter|		after entering another window
|WinLeave|		before leaving a window
|TabEnter|		after entering another tab page
|TabLeave|		before leaving a tab page
|CmdwinEnter|		after entering the command-line window
|CmdwinLeave|		before leaving the command-line window

|InsertEnter|		starting Insert mode
|InsertChange|		when typing <Insert> while in Insert or Replace mode
|InsertLeave|		when leaving Insert mode
|InsertCharPre|		when a character was typed in Insert mode, before
			inserting it

|TextChanged|		after a change was made to the text in Normal mode
|TextChangedI|		after a change was made to the text in Insert mode
|TextYankPost|		after text is yanked or deleted

|ColorScheme|		after loading a color scheme

|RemoteReply|		a reply from a server Vim was received

|QuickFixCmdPre|	before a quickfix command is run
|QuickFixCmdPost|	after a quickfix command is run

|SessionLoadPost|	after loading a session file

|MenuPopup|		just before showing the popup menu
|CompleteDone|		after Insert mode completion is done

|User|			to be used in combination with ":doautocmd"


```
