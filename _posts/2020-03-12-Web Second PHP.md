---
layout:     post                    # 使用的布局（不需要改）
title:      PHP--MD5与SHA1的绕过            # 标题 
subtitle:   My Second question  #副标题
date:       2020-03-12              # 时间
author:        SGQ                   # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - PHP  
    
---

## 开始:
> 趁热打铁！信心重燃！开始接触第二道php题，显然此题花费的时间少了很多。
但收获颇丰！
## 题目：

```php
<?php
require_once('flag.php');

if (isset($_GET['name']) and isset($_GET['password']) && isset($_GET['test'])){
    // ========== Stage 1 ========== 
    $test=$_GET['test']; 
    $test=md5($test); 

    if($test=='0') { 
        print 'You passed stage 1.<br />';
    }
    else{
        print "Game over at stage 1."; 
        exit();
    }

    // ========== Stage 2 ========== 
    if ($_GET['name'] == $_GET['password']){
        print 'Your password can not be your name.';
        exit();
    }
    else if (sha1($_GET['name']) === sha1($_GET['password'])){
        print 'You passed stage 2.<br />';
        print 'Flag: '.$flag;
    }
    else{
        print 'Invalid password';
        exit();
    }
}
echo '<hr />';
show_source(__FILE__);
?> 

```

## 所遇问题：
>1.require_once
>2.md5值如何为0
>3.两个sha1值如何相同


## 解答：
>1.require() 语句的性能与 include() 相类似，都是包括并运行指定文件.
>不同之处在于：对 include() 语句来说，在执行文件时每次都要进行读取和评估；
而对于 require() 来说，文件只处理一次（实际上，文件内容替换 require() 语句）.
这就意味着如果可能执行多次的代码，则使用 require() 效率比较高。
>另外，如果每次执行代码时是读取不同的文件，或者有通过一组文件迭代的循环，就使用 include() 语句.

>_once 后缀表示已加载的不加载 .

>2.PHP在处理哈希字符串时，会利用”!=”或”==”来对哈希值进行比较，它把每一个以”0E”开头的哈希值都解释为0，所以如果两个不同的密码经过哈希以后，其哈希值都是以”0E”开头的，那么PHP将会认为他们相同，都是0.
攻击者可以利用这一漏洞，通过输入一个经过哈希后以”0E”开头的字符串，即会被PHP解释为0，如果数据库中存在这种哈希值以”0E”开头的密码的话，他就可以以这个用户的身份登录进去，尽管并没有真正的密码.
>即：如果md的值是以0e开头的，那么就与其他的0e开头的Md5值是相等的.


>3.分析一下代码，发现GET了两个字段name和password，获得flag要求是：name与password不同，但name和password的sha1值是相同的，虽然看起来这是不可能的，
其实可以利用sha1()函数的漏洞来绕过，如果把这两个字段构造为数组,这样在第一处判断时两数组确实是不同的，但在第二处判断时由于sha1()函数无法处理数组类型，将报错并返回false，if 条件成立，获得flag.



## 完整解答：
>vps1.blue-whale.me:23331/feature/?name[]=1&password[]=2&test=s878926199a

>[常用的0e开头MD5值](https://blog.csdn.net/fengzhantian/article/details/80490629)

## 谢谢您的查阅，欢迎来访！

