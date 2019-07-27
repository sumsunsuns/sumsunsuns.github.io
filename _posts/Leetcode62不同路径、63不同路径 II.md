---
layout:     post                    # 使用的布局（不需要改）
title:      Leetcode62不同路径、、63不同路径 II               # 标题 
subtitle:    #副标题
date:       2019-07-27              # 时间
author:     BY Tony Huang                     # 作者
header-img: img/post-bg-os-metro.jpg  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Leetcode
    - 找工作
---
# 62 不同路径
## 题目描述
一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。
机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。
问总共有多少条不同的路径？
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190727154457202.png)
例如，上图是一个7 x 3 的网格。有多少可能的路径？

说明：m 和 n 的值均不超过 100。

> 示例 1: 
> 输入: m = 3, n = 2 
> 输出: 3 
> 解释: 从左上角开始，总共有 3 条路径可以到达右下角。
> 1. 向右 -> 向右 -> 向下
> 2. 向右 -> 向下 -> 向右
> 3. 向下 -> 向右 -> 向右


> 示例 2: 
> 输入: m = 7, n = 3 
> 输出: 28

## 解题思路：
这道题和之前的斐波那锲数列爬楼梯很相像，一次要么往下走一步，要么往右走一步，用动态规划来做，我们用data[i-1][j-1]表示爬到(i,j)格子的方法，那么data[i][j]=data[i-1][j]+data[i][j-1]
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019072715421531.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjAzNjYxNw==,size_16,color_FFFFFF,t_70)
就像上图一样，走到Finish的方法=你走到上面圆圈的方法+走到下面圆圈的方法

## 代码：

```
class Solution {
public:
    int uniquePaths(int m, int n) {
        int i,j;
       int data[n][m];
        for(i=0;i<n;i++)
        {
            for(j=0;j<m;j++)
            {
                data[i][j]=1;
            }
        }
        for(i=1;i<n;i++)
        {
for(j=1;j<m;j++)
{
    data[i][j]=data[i-1][j]+data[i][j-1];
}
        }
       
    return data[n-1][m-1];
    }
};
```
# 63不同路径II
## 题目描述：
一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。
机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。
现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190727153314975.png)

网格中的障碍物和空位置分别用 1 和 0 来表示。
说明：m 和 n 的值均不超过 100。


> 示例 1:
> 
> 输入: [   [0,0,0],   [0,1,0],   [0,0,0] ] 
> 输出: 2 
> 解释: 3x3 网格的正中间有一个障碍物。
> 从左上角到右下角一共有 2 条不同的路径：
> 1. 向右 -> 向右 -> 向下 -> 向下
> 2. 向下 -> 向下 -> 向右 -> 向右

## 算法思路
跟上面的题类似，用动态规划（Dynamic Programming）来做，我们用data[i][j]表示到达(i-1,j-1)位置的不同路径的数量，那么i和j需要更新的范围就是[1,m][1,n]，状态转移方程为dp[i][j] = dp[i-1][j] + dp[i][j-1]，当遇到障碍物时continue就行了
```
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int i,j;
        if(obstacleGrid.empty()||obstacleGrid[0][0]==1)
        {
            return 0;
        }
        int m=obstacleGrid.size(),n=obstacleGrid[0].size();
        vector<vector<long>>data(m+1,vector<long>(n+1,0));
        data[0][1]=1;
        for(i=1;i<=m;i++)
        {
            for(j=1;j<=n;j++)
            {
                if(obstacleGrid[i-1][j-1]==1)
                {
                    continue;
                }
                data[i][j]=data[i-1][j]+data[i][j-1];
            }
        }
        return data[m][n];
        
    }
};
```


