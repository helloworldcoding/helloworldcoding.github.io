---
layout: post
title: "A introduce to jenkins ci/cd"
description: "a brefie introduction to jenkins ci/cd"
tags: [jenkins,coding,ci/cd]
---

### 立一个flag

想起双十一低价买入的阿里云，一直想搭建一个自己的网站。在这2019年的最后一个周末，终于把这个事情提上提上了议程。    
至于网站的内容，我还没有想太多，前期就是搭建一个博客吧，有一个自己对外的窗口，也支持各路朋友注册使用。   
前端用react 后端暂时用python3吧。   
这个过程中应该会踩坑不少，特别是自己不熟悉的后端和运维相关的知识点。   
这恰好是个很好的实践机会，应该可以输出一系列的填坑文章。   


### pre coding之创建git项目
由于家里的网络对github不够友好，考虑到之前对[coding](www.coding.net)的印象还不错，就在coding上创建了一个项目。   
说句题外话，coding应该是被腾讯收了，有了金主爸爸，对普通用户也放开了诸多权限。   
coding毫无疑问也是支持ssh协议来访问git仓库的，这样就不用每次连接都输入账号和密码。一劳永逸的事情，我最喜欢    
假设当前系统登陆用户是`codinggirl`,要生成该用户的rsa公钥和私钥, 按如下操作进行。这个操作，之后也会用到。
```
ssh-keygen -t rsa -b 4096 -C your_email@example.com
# 然后一路回车 
# 最后默认情况下，会在~/.ssh/ 这个目录下出现两个文件 私钥:id_rsa  公钥:id_rsa.pub
```
把`id_rsa.pub`的内容复制到coding新增加公钥页面中的表单中,保存后，就可以在的自己的电脑上用`codinggirl`用户和coding进行愉快地进行代码交流了。
考虑到未来会持续迭代这个网站，所以考虑到了发布更新的问题。我不能每次都手动去发布，行业里也有很多成熟的解决方案，比如我接下来要说的`jenkins`
关于什么是jenkins，我就不多说了。用过的人，应该很清楚。

### pre coding之jenkins安装配置
之前都是在使用`jenkins`，没有安装配置过。今日在这个jenkins上也折腾了半天。。-\_-   
先说安装,当然是怎么简单，怎么来, 不过要先安装jdk`sudo apt install openjdk`

1. 简单安装
    a. sudo apt install jenkins     
    b. 按照提示操作，比如输入 /var/lib/jenkins/secret.key 的内容，安装常规插件     
    c. 安装coding插件     
    安装过程有点慢，可以在这个过程中去做别的事情     
2. 配置
    a. add credentials  把当前用户(codinggirl)的ssh private_key 添加到jenkins 中，这样jenkins就可以正常从coding拉取代码到 /var/lib/jenkins/workspace/ 下面     
    b. 生成jenkins用户的ssh public_key, 并添加到远程服务器authorized_keys下    
        ⅰ. sudo su jenkins    
        ⅱ. cd ~  # /var/lib/jenkins    
        ⅲ. ssh-keygen   一路回车   
        ⅳ. ssh-copy-id  -i .ssh/id_rsa.pub   www@blog或者直接复制id_rsa.pub的内容到服务器www用户的authorized_keys中 这样jenkins执行shell命令如rsync，就可以同步数据到远程服务器    
    c. 创建一个Freestyle的project,项目名不要随便改动   
    d. Source Code Management 选择git, 然后填入项目的仓库url,如git@git.coding.net:xxxx/xxx.git, Credentials就选择步骤a创建的credentials  
    e. 上面这步成功后，就可以选择分支了，一般会选者默认的master分支, */master     
    f. build Execute Shell  `rsync -rv --exclude=.git/ --exclude=.git*  $WORKSPACE www@blog:~`    


### 猜过的坑

1. build后执行的shell命令，在命令行可以正常执行，但是jenkins在构建后，提示
```
Host key verification failed.
rsync: connection unexpectedly closed (0 bytes received so far) [sender]
```
原因：jenkins在构建时，执行rsync命令的用户是jenkins，不是codinggirl。所以需要给jenkins这个用户创建rsa的公钥和私钥,并将公钥加入到阿里云服务器的authorized_keys中


这样我就只要每次push代码，然后在jenkins中构建，就可以把代码同步到服务器了。当然前端和后端我会分为两个项目，到时候在分享前后端部署的问题。   

周六爬了梧桐山，山顶依旧有"演唱会", 主唱依旧蒙着脸，带着墨镜，与两年前不同，不再是一个人，而是一个乐队----纵声乐队。唱完了一首歌，主唱说，他坚持来山顶唱歌，是想通过这种方式锻炼自己....,后来，他开始卖自己的演唱会的票，好像只要20块。希望他们实现自己的音乐梦想，走向更大的舞台。





