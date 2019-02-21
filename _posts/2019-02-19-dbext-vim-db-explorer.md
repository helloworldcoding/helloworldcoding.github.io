---
layout: post
title: "dbext an awesome database expolorer" 
tags: [vim,database,navicat]
description: "say goodbey to those bad database expolorer software" 
---

可能是最适合开发的数据库客户端
====

#### 支持的数据库类型:

    SAP Sybase SQL Anywhere (SA/ASA)
    SAP Sybase IQ
    SAP Sybase Adaptive Server Enterprise (ASE)
    SAP HANA
    SAP Sybase UltraLite (UL)
    Oracle
    Oracle RDB
    SQL Server
    MySQL
    PostgreSQL
    DB2
    Firebird
    Ingres
    Interbase
    SQLite

本文以MySQL为例，介绍一下该插件的功能。

### 安装&配置
	
	Lets test the connection, run the following command (Note the capital S): >
    `:Select * from customer`
    Select, Update, Insert, Delete, Call, Drop, Alter, Create

### 功能介绍

#### connect

	使用快捷键 ：`<leader>sbp` leader键默认是`\`,  sbp(sql buffer prompt)
    使用ex命令：`:DBPromptForBufferParameters`
	然后选择要连接的数据库类型

#### execute 

	把光标置于sql语句中的任意位置（sql语句是一行）
	`<leader>sel`  sql execute line

	执行多行sql
	`<leader>se`  sql execute 
	`:DBExecSQLUnderCursor`

	执行选中的sql
	`:DBExecVisualSQL`
	<leader>se  和多行执行一样

	从表中查选,光标在表名称的单词上
	`<leader>st`  sql table
	`:DBSelectFromTable`
	执行的sql是：select * from table_name
	
	带条件的查询
	`<leader>stw`
	`:DBSelectFromTableWithWhere`
	会提示你输入where条件， 比如 id > 1 and name like "zhangsan"
	执行的sql：select * from table_name where id > 1 and name like "zhangsan"

	等待输入表名查询
	`<leader>sta`
	`:DBSelectFromTableAskName`
	会提示你输入你要查询的表名
	
	高亮选择表名查询
	`<leader>st`
	`:'<,'>DBSelectFromTable`

#### Describing objects
	
	表结构
	`:DBDescribeTable`
	`\sdt` sql describe table
	
	存储过程procedures
    `:DBDescribeProcedure`
    `\dp`  d (describe) p (procedure)
	


