---
layout: post
title: "vim and tmux" 
tags: [vim,tmux]
description: "" 
---

Learn some skills
====

### open two or  more files with vim

vim 1.py 2.py -O  

	-O split the window left/right
	-o split the window up/down

:windo difft  

// check the difference between the two files, just like `git diff`
	

### edit the last modified file

	vim $(ls -t | head -1)
	
	or you can set an alias,update your ~/.bashrc
	`alias vim1='vim $(ls -t | head -1)'`

### Jumplist & Changelist

change jump through buffers

`Ctrl+o`/`Ctrl+i`  cycel through: jumps<br>
`g;`/`g,`  cycel through:changes

`gf` goto the file when the cursor is on the filepath


### window routing

:vert sf file 
:sf {FIILE} // spilt window and :find {FILE}
`ctrl+x` routing the window
	
