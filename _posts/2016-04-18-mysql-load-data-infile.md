---
layout:     post
title : "mysql load data infile"
subtitle:   " \"从文件向数据库中导入数据\""
date:       2016-04-18 12:00:00
author:     "YuHuo"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - mysql
---
## 前言  

把文件数据导入数据库  

---

## 使用  

**load data infile** 语句从一个文本文件中以很高的速度读入一个表中。使用这个命令之前，mysqld进程（服务）必须已经在运行。为了安全原因，当读取位于服务器上的文本文件时，文件必须处于数据库目录或可被所有人读取。另外，为了对服务器上文件使用load data infile，在服务器主机上你必须有file的权限。

#### 基本语法  

    load data [low_priority][local] infile 'file_name txt' [replace|ignore]  
    into table tbl_name  
    [fields [terminated by 't'] [OPTIONALLY] enclosed by ''] [escaped by '\']]  
    [lines terminated by 'n'] [ignore number lines] 
    [(col_name,   )]  

#### 注意
   
1  如果你指定关键词`low_priority`，那么MySQL将会等到没有其他人读这个表的时候，才把插入数据。可以使用如下的命令：  
   
	load data  low_priority infile "/home/mark/data sql" into table Orders;  
	
2  如果指定`local`关键词，则表明从客户主机读文件。如果local没指定，文件必须位于服务器上。  

3  `replace`和`ignore`关键词控制对现有的唯一键记录的重复的处理。如果你指定replace，新行将代替有相同的唯一键值的现有行。如果你指定ignore，跳过有唯一键的现有行的重复行的输入。如果你不指定任何一个选项，当找到重复键时，出现一个错误，并且文本文件的余下部分被忽略。例如：  

	load data  low_priority infile "/home/mark/data sql" replace into table Orders;
 
4 分隔符  

(1) `fields`关键字指定了文件记段的分割格式，如果用到这个关键字，MySQL剖析器希望看到至少有下面的一个选项：   
`terminated by`分隔符：意思是以什么字符作为分隔符  
`enclosed by`字段括起字符  
`escaped by`转义字符  
`terminated by`描述字段的分隔符，默认情况下是tab字符（\t）   
`enclosed by`描述的是字段的括起字符。  
`escaped by`描述的转义字符。默认的是反斜杠（backslash：\ ）   

例如：  

	load data infile "/home/mark/Orders txt" replace into table Orders fields terminated by',' enclosed by '"';  

(2)`lines` 关键字指定了每条记录的分隔符默认为'\n'即为换行符
如果两个字段都指定了那fields必须在lines之前。如果不指定fields关键字缺省值与如果你这样写的相同： fields terminated by'\t' enclosed by ’ '' ‘ escaped by'\\'
如果你不指定一个lines子句，缺省值与如果你这样写的相同： lines terminated by'\n'
例如：  

	load data infile "/jiaoben/load.txt" replace into table test fields terminated by ',' lines terminated by '/n';  

5  `(col_name,   )` 按指定的列把文件导入到数据库中. 当我们要把数据的一部分内容导入的时候,需要加入一些栏目（列/字段/field）到MySQL数据库中，以适应一些额外的需要。比方说，我们要从Access数据库升级到MySQL数据库的时候
下面的例子显示了如何向指定的栏目(field)中导入数据： 

	load data infile "/home/Order txt" into table Orders(Order_Number, Order_Date, Customer_ID);  

6  当在服务器主机上寻找文件时，服务器使用下列规则： 
> *（1）如果给出一个绝对路径名，服务器使用该路径名。 
> *（2）如果给出一个有一个或多个前置部件的相对路径名，服务器相对服务器的数据目录搜索文件。  
> *（3）如果给出一个没有前置部件的一个文件名，服务器在当前数据库的数据库目录寻找文件。 
例如： /myfile txt”给出的文件是从服务器的数据目录读取，而作为“myfile txt”给出的一个文件是从当前数据库的数据库目录下读取。



## 场景
    
在windows环境下，向数据库中批量导入文本文件数据   

1.生成批量导入sql语句   
  

	for /r %i in (*.log) do (echo  load data infile "C:/Users/Administrator/Desktop/chip/%~ni.log" into table wish_cost_chip_log  fields terminated by ',' lines terminated by '\n'; >>  load.sql)  

生成 load.sql 中的语句格式如下  

	load data infile "C:/Users/Administrator/Desktop/chip/2016-03-28.log" into table wish_cost_chip_log  fields terminated by ',' lines terminated by '\n'; 
	load data infile "C:/Users/Administrator/Desktop/chip/2016-03-29.log" into table wish_cost_chip_log  fields terminated by ',' lines terminated by '\n'; 
	...

dos环境下的`for` 语句的使用 详见 [dos下的for语句](./)  

2.使用 `mysql source [sql文件]` 导入数据  
