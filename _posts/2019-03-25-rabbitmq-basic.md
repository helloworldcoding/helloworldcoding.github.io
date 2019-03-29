---
layout: post
title: "Note of Rabbitmq basic" 
tags: [rabbitmq,message queue,rabbitmqctl]
description: "" 
---

Rabbitmq 兔子消息队列
====

### 查看队列长度

	rabbitmqctl  list_queues | grep _some_keyword | awk -F' ' '{print $2}' // 默认是/这个vhosts
	rabbitmqctl -p ykupgrader_vhost list_queues | grep _some_keyword | awk -F' ' '{print $2}' // -p 指定vhost
