---
layout:     post                    # 使用的布局（不需要改）
title:      剑指offer-数组中重复的数字、数组中只出现一次的数字（内含STL map讲解）               # 标题 
subtitle:    #副标题
date:       2019-06-05              # 时间
author:     BY Tony Huang                     # 作者
header-img: img/post-bg-os-metro.jpg  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 剑指offer
    - 面试题
    - 找工作
    - map
---
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019060210315020.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjAzNjYxNw==,size_16,color_FFFFFF,t_70)
# 欢迎大家访问我的博客[Tony's Blog](https://yunfanzhilu.github.io/)，一起站在巨人的肩膀上！
# 数组中重复的数字
# 题目描述：
在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。
# 算法思想：
建立map映射，遍历一边数组，记录各个数字出现的次数，一旦有数字出现2次，就证明这个数字是原数组第一个重复的数字.
# 代码：

```
class Solution {
public:
    // Parameters:
    //        numbers:     an array of integers
    //        length:      the length of array numbers
    //        duplication: (Output) the duplicated number in the array number
    // Return value:       true if the input is valid, and there are some duplications in the array number
    //                     otherwise false
    bool duplicate(int numbers[], int length, int* duplication) {
        int i,len,j,mn;
        if(length<=0)//注意代码鲁棒性
        {
               return false;
        }
        
        map<int,int>temp;
        for(i=0;i<length;i++)
        {
            j=++temp[numbers[i]];//统计原数组中每个数出现的次数
            if(j==2)
            {
                duplication[0]=numbers[i];//这个duplication是一个数组，用指针指向数组首地址，只有一个值
                return true;
                break;
            }
           
        }
        return false;
    }
};
```
# 注意：这个duplication是一个数组，用指针指向数组首地址，只有一个值
# 数组中只出现一次的数字
## 题目描述：
一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字
## 算法思想：
建立map表，两次for循环遍历，一次统计原数组中各个数字出现的次数，第二次for循环，遍历只出现一次的数字
## 代码：

```
class Solution {
public:
    void FindNumsAppearOnce(vector<int> data,int* num1,int *num2) {

         int i,j,k=1,num;
        map<int,int>m;
        vector<int>temp;
        int len;
        len=data.size();
        for(i=0;i<len;i++)
        {
            m[data[i]]++;
         
        }
        for(j=0;j<data.size();j++)//这里要写原数组data.size(),不能写哈希表m.size()
        {
            if(m[data[j]]==k)
            {
                temp.push_back(data[j]);
            }
        }
         num1[0]=temp[0];
        num2[0]=temp[1];
    }
};
```

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

### （2）插入元素

 #### - 使用下标
 
```
//用"array"方式插入
    mapStudent["r123"] = "student_first";
    mapStudent["r456"] = "student_second";
```
>在map中使用下标访问不存在的元素将导致在map容器中添加一个新的元素。
#### - 使用insert函数
 
 - `m.insert(e)`
 - `m.insert(beg,end)`
 - `m.insert(iter,e)`
 
上述的e一个value_type类型的值。beg和end标记的是迭代器的开始和结束。

```
m.insert(make_pair(i, i));
```

### （3）查找

若只是查找该元素是否存在，可以使用函数count(k)，该函数返回的是k出现的次数；若是想取得key对应的值，可以使用函数find(k)，该函数返回的是指向该元素的迭代器。
**注意：上述采用下标的方法读取map中元素时，若map中不存在该元素，则会在map中插入。**

```
//map<int,int> m;
//int key;
auto it = m.find(key);
if( it != m.end())
    return it->second;
```


```
//map<int,int> m;
//int key;
if(m.count(key) > 0)
    return m[key];
```

### （4）删除与清空

 - `m.erase(k)//删除的是m中键为k的元素，返回的是删除的元素的个数`
 - `m.erase(p)//删除的是迭代器p指向的元素，返回的是void`
 - `m.erase(b,e)//删除的是迭代器b和迭代器e范围内的元素，返回void`

```
//迭代器刪除
iter = mapStudent.find("r123");
mapStudent.erase(iter);
 
//用关键字刪除
int n = mapStudent.erase("r123");//如果刪除了會返回1，否則返回0
 
//用迭代器范围刪除 : 把整個map清空
mapStudent.erase(mapStudent.begin(), mapStudent.end());
//等同于mapStudent.clear()
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
# 参考链接
[C/C++——map的基本操作总结](https://blog.csdn.net/google19890102/article/details/51720305)
[C++使用: C++中map的基本操作和用法](https://www.cnblogs.com/empty16/p/6395813.html)
[C++ map使用详细版](https://blog.csdn.net/baidu_30594023/article/details/81913967)

