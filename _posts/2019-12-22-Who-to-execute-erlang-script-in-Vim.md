---
layout: post
title: "How to execute an erlang script without exit Vim"
description: "make it more easily when you debug a erlang script"
tags: [erlang, vim,execute ]
---

最近打算下定决心学习使用`erlang`, 不过我有点不习惯在`erlang shell`和vim之间来回切换。   
想了想，干脆优化一下这个重复的过程吧。就像我配置了python的执行快捷键一样，每次只要按下`F6`，
就会执行当前编辑的python脚本，并新开一个terminal,输出程序正常打印的信息或错误信息 

## 编写Hello world 脚本
```
-module(hello).
-export([say/0, main/0, hi/1]).


hi(Name) ->
    io:format("hi ~s~n",Name).

say() ->
    io:format("hello world~n").

main()->
    io:format("main~n"),
    say().
```


## Erlang执行脚本的方法

1. 在`erlang shell`中执行
    `erl` 进入erlang shell    
    $ erl    
    Erlang/OTP 22 [erts-10.5] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:1] [hipe]     
    Eshell V10.5  (abort with ^G)    
    1> c(hello).    
    {ok,hello}   
    2> hello:say().    
    hello world     
    ok    

2. 在shell中执行
    $ erlc  hello.erl    
    $ erl -noshell -s hello say -s init stop
    erlc从命令行和启动了erlang编译器,编译器编译了hello.erl    
    并生成一个名为hello.beam的目标代码文件
    erl -noshell ... 加载了hello模块并执行了hello:say()函数，最后，执行init:stop(),终止了erlang会话


这两种都比较麻烦,有没有更简单一点的方法呢？作为一个偷懒的程序猿，我肯定不喜欢这样重复的

## 我的解决方法

1. 在`/usr/local/bin/`下创建一个可执行的bash脚本,假设名称是`exec_erl`，内容如下    
```
#!/bin/bash

erl_file_name=$1
shift
fun=$1
shift
if [ -z "$fun" ]; then
    fun=main
fi
erl_mode="${erl_file_name%.*}"
erlc ${erl_file_name}
erl -noshell -s $erl_mode $fun $@ -s init stop

```
2. 修改vim配置文件    
```
augroup filetype_erlang
    autocmd!
    autocmd Filetype erlang nnoremap <buffer> <F6> :w<CR>:ter   exec_erl %  <CR>
augroup END

```

## 小结

在编写一个elrang脚本后，想快速验证,只需要再多写一个`main`函数并把他export出来,然后再`main`中调用你的测试方法，
按下`F6`就可以运行了。

可以愉快地debug脚本了.
其他需要编译的语言，也可以用这个类似的方法解决，不过，应该都比erlang的简单一些

还有一个好处，如果想验证某个方法,可以直接在执行`exec_erl  filename.erl  functionname   param1   param2 `  
例如,我想执行hello.erl中say()  
`exec_erl hello.erl say`     
如果想执行hi   
`exec_erl hello.erl hi zhangsan`   







