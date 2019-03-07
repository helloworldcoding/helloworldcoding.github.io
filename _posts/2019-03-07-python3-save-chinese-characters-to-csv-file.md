---
layout: post
title: "Save  Chinese characters in csv files with Python3"
tags: [csv,中文乱码,python3,utf-8,utf-8]
description: "python3生成中文不乱码的csv文件" 
---


导出一些数据给运营同学
===
### 几点要求

- 一个脚本搞定，尽可能少地依赖其他包
- 可以用windows下的excel打开,中文不乱码
- 可以是csv文件，也可以用`\t`分割，生成xls文件

excel的坑
===

	在程序中能够正常输出中文，但是导出到文件后使用excel打开是出现中文乱码是因为excel能够正确识别用gb2312、gbk、gb18030或utf_8 with BOM 编码的中文，如果是utf_8 no BOM编码的中文文件，excel打开会乱码。

```python3
#!/usr/bin/python3
#coding:utf-8
import pymysql
import json

try:
    db = pymysql.connect("localhost","usrname","password","dbname",4040)
    cursor = db.cursor()
    sql = "select org_code,command_return from upgrade_log where upgrade_id=1060 and stage_id=68"
    cursor.execute(sql)
	# 关键是设置encoding="utf_8_sig",这样excel打开才不会出现中文乱码
	# 这样生成的文件就是有BOM头，excel才能正常处理中文
    fh = open("400.csv","w",encoding="utf_8_sig") 
    for row in cursor:
        org_code = row[0]
        info =json.loads(row[1]) # row[1]是一段json
        if len(info[2:]) > 0:
            for item in info[2:]:
                line = item.replace("\t",',')
                if line[-4:] != "NULL":
                    line = line+", "
                line = org_code+","+line +"\n"
                fh.write(line)
        else:
            print(org_code)

        
except Exception as err:
    print(err)

fh.close()
cursor.close()
db.close()
```
