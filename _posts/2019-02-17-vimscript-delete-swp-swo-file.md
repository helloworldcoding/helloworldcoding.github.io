---
layout: post
title: "Vim delete swp&swo file"
tags: [vim,swp,swo,delete]
description: "delete the current file's swp an swo file"
---

写了一个小函数，来删除vim的swp和swo文件。

在normal模式下，输入\ds就可以删除该文件对应的swp和swo文件。
```
" 删除当前文件对应的swp和swo文件
function! DeleteSpaw()
	let l:fname = fnameescape(expand('%:t'))
	let l:swp = '.'.l:fname.'.swp'
	let l:swo = '.'.l:fname.'.swo'
	if CheckFile(l:swp)
		echom DeleteFile(l:swp) == 1 ? l:swp.' delete successfully' : l:swp.' delete faild '
	endif
	if CheckFile(l:swo)
		echom DeleteFile(l:swo) == 1 ? l:swo.' delete successfully' : l:swo.' delete faild '
	endif
endfunction

function! DeleteFile(filename)
	let l:del_result = delete(expand(a:filename))
	return l:del_result == 0 ? 1 : 0
endfunction

function! CheckFile(filename)
	" 对于不可以读取的文件，换成glob
	if filereadable(expand(a:filename)) 
		return 1
	else
		return 0
	endif
endfunction

" 调用删除swp,swo文件函数
nnoremap <leader>ds :call DeleteSpaw()<cr>
```

#### 小结

- 获取文件名expand('%:t'),参考`help filename-modifiers`
- 文件相关的函数，`filereadable(), delete()`
- 函数内部对参数使用，a:param
