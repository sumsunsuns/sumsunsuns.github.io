---
layout:     post                    # 使用的布局（不需要改）
title:      C++ STL关联性容器set与map基本操作简述               # 标题 
subtitle:    #副标题
date:       2019-06-07              # 时间
author:     BY Tony Huang                     # 作者
header-img: img/post-bg-e2e-ux.jpg  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - STL
    - map
    - set
---

# 欢迎大家访问我的博客[Tony's Blog](https://yunfanzhilu.github.io/)，一起站在巨人的肩膀上！
>在之前一篇博客[C++标准库（STL）栈（stack）、队列（queue）、双向队列（deque）、容器（vector）的基本操作](https://mp.csdn.net/mdeditor/88744518#)中，详细说明了顺序容器vector、stack、queue、deque的使用，本期来说说STL中关联性容器set与map的使用。
# map简述
## 概念
map是STL中很有用的容器，**底层为红黑树**，它提供一对一(key,value)的hash,其中key只能出现一次，value为该key的值。
红黑树具有对数据自动排序的功能。在map内部所有的数据都是有序的
## 用法
### （1）变量声明

```
map<k, v> m;//定义了一个名为m的空的map对象；
map<k, v> m(m2);//创建了m2的副本m，
map<k, v> m(b, e);//创建map对象m，并且存储迭代器b和e范围内的所有元素的副本
```

### （2）插入元素(下标插入与inert插入) 
```
map<int, int>mp;
	mp[0] = 1;
	mp[1] = 2;
	mp[2] = 3;
```
相当于

```
	map<int, int>mp;
	mp.insert(make_pair(0, 1));
	mp.insert(make_pair(1, 1));
	mp.insert(make_pair(2, 3));
```

>无论你插入的顺序是什么，map会自动排序。另外key的值是唯一的，之前定义了key，后面想插入相同的key就不行了

### （3）查找

 - **如果要直接查找元素在map中出现过多少次，那么可以使用count函数**
  ```
//map<int,int> m;
//int key;
if(m.count(key) > 0)
    return m[key];//返回的是key对应的value
```
*注意这里的count是直接查找键值（key），而且因为map中会自动对相同键值去重……所以count相当于只会告诉你这个元素是否有出现过。如果想使用count计算元素出现次数可以使用multimap。*
 - **map还有find（）函数，可以返回指向所查找元素的迭代器，如果没有此元素就返回指向map尾部的迭代器。注意这里要用迭代器**
 
还可以使用lowerbound和upperbound进行查找，很方便
```
//map<int,int> m;
//int key;
auto iter= m.find(key);//auto相当于map <int,int> :: iterator iter;
if( iter == m.end())//这句话的意思是没找到的话就干什么
    return iter->second;//返回的是key相应的value
```
### （4）删除与清空

 - `m.erase(k)//删除的是m中键为k的元素，返回的是删除的元素的个数`
 - `m.erase(p)//删除的是迭代器p指向的元素，返回的是void`
 - `m.erase(b,e)//删除的是迭代器b和迭代器e范围内的元素，返回void`
 
 

```
mp.erase(2);
```

```
mp.erase(mp.begin());//显然begin函数返回的是一个迭代器！！
```

```
  map <int,int> mp;
    rep(i,0,19) mp.insert(make_pair(i,i));
        map <int,int> :: iterator itf;
    itf = mp.upper_bound(15);
    mp.erase(mp.begin(),itf);    
```

### （5）输出

```
map<int, int>::iterator it;
 for (it = mp.begin(); it != mp.end(); it++)
    {
  printf("%d->%d\n", it->first, it->second);
      }
```
>迭代器iterator就是一种智能指针。它对原始指针进行了封装，而且提供一些等价于原始指针的操作，做到既方便又安全，迭代器iterator提供了对顺序或关联容器类型中的每个元素进行连续访问的方法，很好用。

# set简述
set是一种关联式容器，特性如下：

 - 底层实现为红黑树
 - 所有元素只有key没有value，value就是key(这是与map的不同)
 - 不允许出现简直重复
 - 所有元素都会被自动排序
 - 不能用迭代器来改变set的值，因为set的值就是健
## 声明

```
set<int>q;
```

## 插入

```
q.insert(7);
q.insert((node){a,b,c}//如果你自定义一个结构体，就可以这么传（和平时往队列里面压一样）
```
## 查找、删除
与map中使用一样，这里不再赘述
## 打印

```
set<int>::iterator iter;
	for (iter = q.begin(); iter != q.end(); iter++)
	{
		cout << *iter;//这里注意与map的区别，不能使用iter->first
	}
```

# 参考链接
[C/C++——map的基本操作总结](https://blog.csdn.net/google19890102/article/details/51720305)
[C++使用: C++中map的基本操作和用法](https://www.cnblogs.com/empty16/p/6395813.html)
[C++ map使用详细版](https://blog.csdn.net/baidu_30594023/article/details/81913967)
[C++ set和map的简单使用](https://www.cnblogs.com/captain1/p/9477916.html)
[C++中map和set的使用与区别](https://blog.csdn.net/zy20150613/article/details/78693579)
