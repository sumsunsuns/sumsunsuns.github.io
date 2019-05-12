---
layout:     post                    # 使用的布局（不需要改）
title:      C++标准库（STL）vector、栈、队列的基本操作             # 标题 
subtitle:   详细讲述了STL中stack、queue、vector的使用 #副标题
date:       2019-05-08             # 时间
author:     BY Tony_Huang                      # 作者
header-img: img/1.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 找工作
---

# C++标准库
在C++标准中，STL有13个头文件，写C++程序时添加这些头文件就可以让我们方便地使用这些数据结构,编程效率可以得到很大的提高。
C++标准库英文名：Standard Template Libary，是由惠普实验室开发的一系列软件的统称，是一系列标准模板的组合，比较好用，可以直接中加入这些库函数文件使用这些好用的数据结构。
      1、 #include<stack>   //栈的头文件
     stack<Type>T，Type为数据类型，既可以是整形（int），浮点型（float）也可以是二叉树指针(TreeNode *)等等
 基本操作：

    s.empty()//如果栈为空返回true，否则返回false  
    s.size()//返回栈中元素的个数  
    s.pop()//删除栈顶元素但不返回其值  
    s.top()//返回栈顶的元素，但不删除该元素  
    s.push(data)//在栈顶压入新元素 
       2、#include<queue>//队列的头文件
  queue<Type>Tve，Type为数据类型，既可以是整形（int），浮点型（float）也可以是二叉树指针(TreeNode *)等等
  基本操作：

    q.empty()// 如果队列为空返回true，否则返回false  
    q.size() // 返回队列中元素的个数  
    q.pop()  //删除队列首元素但不返回其值  
    q.front()  // 返回队首元素的值，但不删除该元素  
    q.push(data) //在队尾压入新元素 
    q.back() //返回队列尾元素的值，但不删除该元素
      
 3、#include<vector>//容器的头文件
 vector<Type>T，Type为数据类型，既可以是整形（int），浮点型（float）也可以是二叉树指针(TreeNode *)等等
 vector可以看做比较好用的数组形式，实际上在C++中已经很少应用传统的数组了，例如像int a[10]这种了，vector这种数组形式可以让我们方便地实时添加和删除元素例如vector<int>b，另外二维数组也可以用vector例如vector<vector<int>>a，就定义了一个vector形式的二维数组。
 a.push_back(b)就填充二维数组的每一行了
 下面是vector的基本操作
 

```
v.size();//容量大小
v.push_back()//添加元素
v.pop_back()//删除元素
v.insert()//在任意位置添加元素
v.erase()//在任意位置删除元素
v.swap()//交换元素
v.assigin//多个元素赋值
```
