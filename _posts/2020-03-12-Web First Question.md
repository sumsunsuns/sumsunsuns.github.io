---
layout:     post                    # 使用的布局（不需要改）
title:      PHP--Get与Post方法            # 标题 
subtitle:   Web First Question #副标题
date:       2020-03-12              # 时间
author:        SGQ                   # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - PHP  
    
---

## 开始:

近一年的备考，一些基础知识已经遗忘，经过复习，我对PHP的相关知识又渐渐熟悉。
虽然此题做后感觉竟如此简单，但还是遇到了一些坎坷。

## 题目:

```php
<?php
	if(isset($_GET['key'])){
		if($_GET['key'] == 'areyousure'){
			echo 'For this exercise, flag is: ******';
		}
	}
?>

```

## 所遇问题：
**1.** `isset()`函数

**2.** `$_GET`变量

**3.** 如何利用？


## 解答：
**1.** `isset()` 函数用于检测变量是否已设置并且非 NULL，
若使用 `isset()` 测试一个被设置成 NULL 的变量，将返回 FALSE。
说白了此题就是检测GET是否得到值。

**2.** 在 PHP 中，预定义的` $_GET `变量用于收集来自` method="get"` 的表单中的值。
同理 预定义的` $_POST `变量用于收集来自` method="post"` 的表单中的值。

**3.** 经过分析代码，可知当GET提交的值`key=areyousure`，即可得到相应的flag。
我一开始是打算安装火狐新版的hackerbar发现并不好用，旧版hackerbar 可点击[这里](https://www.jianshu.com/p/d96e90f5a812)下载.



## 完整解答：

**vps1.blue-whale.me:23331/php0/?key=areyousure**

同理post方法,可利用插件hackbar的post data,输入key=areyousure.

## 谢谢您的查阅，欢迎来访！


