---
layout: post
title: "Plantuml Introduce"
description: "plantuml"
tags: [plant,uml]
image:
  path: /images/abstract-6.jpg
  feature: abstract-6.jpg
---

> 慢慢发现在诸多技能中，coding是最不应该花时间的。
> 在代码编写前，和代码编写后的事情，往往才是重要的。
> 想清楚你要做什么，要怎么做，以及之后要达成什么样的目标，检验结果是否符合预期
> 这些事情，都应该比coding要重要。
> 没有人愿意去读你的代码，除非没办法。
> 想写一系列的pre coding 和 post coding 的文章。今天开个头，希望能持续写下去

#### UML--让你的需求清晰的法宝

    首先说明，我不用图形界面拖拽控件来画流程图。诸如`Visio` `Precess On`我一律不用。
    原因很简单，我认为拖拽的效率非常低，你大量的精力都花在元素是否对其，鼠标和键盘之间来回切换,
    你反而不能集中精力去思考逻辑。还有一个原因，不利于复用和修改。

#### plantuml

[plantuml](https://plantuml.com/index "plantuml")是一个开源的工具，你可以用它来画流程图，用例图
类图等等。最令人满意的是，你可以用写代码的模式来“画”uml图。
比如：

1.创建一个demo.uml的文件,用你习惯的编辑器，编写如下内容：
```
    @startuml
    client->server: syn=1 seq=x
    note left
        客户端发送syn报文,
        发送序列号为x
    end note
    client<-server: syn=1 ack=x+1 seq=y
    note right
        服务端接受请求， 回复syn+ack报文，
        并置发送序列号为y,确认序列号为x+1
    end note
    client->server: ack=y+1 seq=z
    note left
        客户端发送ack报文,并置发送序列号为z
        确认序列号为y+1
    end note
    @enduml
```
2.下载plantuml.jar包
```
    http://sourceforge.net/projects/plantuml/files/plantuml.jar/download
```
3.把`plantuml.jar`移动到你乐意的路径下,并创建一个可执行文件plantuml文件
```
    linux下参考如下操作:
    mv ./plantuml.jar   /usr/local/bin/plantuml.jar
    touch /usr/local/bin/plantuml
    echo "#!/bin/bash" >> /usr/local/bin/plantuml
    echo "jar -jar /usr/local/bin/plantuml.jar $@" >> /usr/local/bin/plantuml
    chmod +x  /usr/local/bin/plantuml 
```
4.查看plantuml的帮助`plantuml -h`,结果如下
```
Usage: java -jar plantuml.jar [options] -gui
	(to execute the GUI)
    or java -jar plantuml.jar [options] [file/dir] [file/dir] [file/dir]
	(to process files or directories)

You can use the following wildcards in files/dirs:
	*	means any characters but '/'
	?	one and only one character but '/'
	**	means any characters (used to recurse through directories)

where options include:
    -gui		To run the graphical user interface
    -tpng		To generate images using PNG format (default)
    -tsvg		To generate images using SVG format
    -teps		To generate images using EPS format
    -tpdf		To generate images using PDF format
    -tvdx		To generate images using VDX format
    -txmi		To generate XMI file for class diagram
    -tscxml		To generate SCXML file for state diagram
    -thtml		To generate HTML file for class diagram
    -ttxt		To generate images with ASCII art
    -tutxt		To generate images with ASCII art using Unicode characters
    -tlatex		To generate images using LaTeX/Tikz format
    -tlatex:nopreamble	To generate images using LaTeX/Tikz format without preamble
    -o[utput] "dir"	To generate images in the specified directory
    -DVAR1=value	To set a preprocessing variable as if '!define VAR1 value' were used
    -Sparam1=value	To set a skin parameter as if 'skinparam param1 value' were used
    -r[ecurse]		recurse through directories
    -I/path/to/file	To include file as if '!include file' were used
    -I/path/to/*.puml	To include files with pattern
    -charset xxx	To use a specific charset (default is UTF-8)
    -e[x]clude pattern	To exclude files that match the provided pattern
    -metadata		To retrieve PlantUML sources from PNG images
    -nometadata		To NOT export metadata in PNG/SVG generated files
    -checkmetadata		Skip PNG files that don't need to be regenerated
    -version		To display information about PlantUML and Java versions
    -checkversion	To check if a newer version is available for download
    -v[erbose]		To have log information
    -quiet		To NOT print error message into the console
    -debugsvek		To generate intermediate svek files
    -h[elp]		To display this help message
    -testdot		To test the installation of graphviz
    -graphvizdot "exe"	To specify dot executable
    -p[ipe]		To use stdin for PlantUML source and stdout for PNG/SVG/EPS generation
    -encodesprite 4|8|16[z] "file"	To encode a sprite at gray level (z for compression) from an image
    -computeurl|-encodeurl	To compute the encoded URL of a PlantUML source file
    -decodeurl		To retrieve the PlantUML source from an encoded URL
    -syntax		To report any syntax error from standard input without generating images
    -language		To print the list of PlantUML keywords
    -checkonly		To check the syntax of files without generating images
    -failfast		To stop processing as soon as a syntax error in diagram occurs
    -failfast2		To do a first syntax check before processing files, to fail even faster
    -pattern		To print the list of Regular Expression used by PlantUML
    -duration		To print the duration of complete diagrams processing
    -nbthread N		To use (N) threads for processing
    -nbthread auto	To use 4 threads for processing
    -timeout N		Processing timeout in (N) seconds. Defaults to 15 minutes (900 seconds).
    -author[s]		To print information about PlantUML authors
    -overwrite		To allow to overwrite read only files
    -printfonts		To print fonts available on your system
    -enablestats	To enable statistics computation
    -disablestats	To disable statistics computation (default)
    -htmlstats		To output general statistics in file plantuml-stats.html
    -xmlstats		To output general statistics in file plantuml-stats.xml
    -realtimestats	To generate statistics on the fly rather than at the end
    -loopstats		To continuously print statistics about usage
    -splash		To display a splash screen with some progress bar
    -progress		To display a textual progress bar in console
    -pipeimageindex N	To generate the Nth image with pipe option
    -stdlib		To print standard library info
    -extractstdlib	To extract PlantUML Standard Library into stdlib folder
    -filename "example.puml"	To override %filename% variable
    -preproc		To output preprocessor text of diagrams
    -cypher		To cypher texts of diagrams so that you can share them

If needed, you can setup the environment variable GRAPHVIZ_DOT.
```

5.把刚才写的uml文件输出为png或其他格式
    `plantuml -tpng test.uml` 图片如下：
<figure>
    <a href=''><img src="/images/tcp_demo.png"></a>
</figure>

输出一个文本的图`plantuml -tutxt test.uml`




                                  ┌──────┐             ┌──────┐                                    
                                  │client│             │server│                                    
                                  └──┬───┘             └──┬───┘                                    
               ╔════════════════════╗│     syn=1 seq=x    │                                        
               ║客户端发送syn报文, ░║│ ───────────────────>                                        
               ║发送序列号为x       ║│                    │                                        
               ╚════════════════════╝│                    │                                        
                                     │ syn=1 ack=x+1 seq=y│  ╔════════════════════════════════════╗
                                     │ <───────────────────  ║服务端接受请求， 回复syn+ack报文， ░║
                                     │                    │  ║并置发送序列号为y,确认序列号为x+1   ║
                                     │                    │  ╚════════════════════════════════════╝
╔═════════════════════════════════════╗│    ack=y+1 seq=z   │                                        
║客户端发送ack报文,并置发送序列号为z ░║│ ───────────────────>                                        
║确认序列号为y+1                      ║│                    │                                        
╚═════════════════════════════════════╝┴───┐             ┌──┴───┐                                    
                                  │client│             │server│                                    
                                  └──────┘             └──────┘                                    
