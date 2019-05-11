---
layout:     post                    # 使用的布局（不需要改）
title:      -offer-按之字形顺序打印二叉树               # 标题 
subtitle:   剑指offer #副标题
date:       2019-05-11              # 时间
author:     BY Tony Huang                      # 作者
header-img: 1.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 面试
    - 找工作
---
这道题目中，我们采用按队列打印二叉树算法的改进算法就好了
代码：
/*
struct TreeNode {
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
    TreeNode(int x) :
            val(x), left(NULL), right(NULL) {
    }
};
*/
class Solution {
public:
    vector<vector<int> > Print(TreeNode* pRoot) {
        vector<vector<int>>result;
       //TreeNode* TempNodeLeft,TempNodeRight;
        int number=2,num1=0,num2=0;
        stack<TreeNode *>s1;
        stack<TreeNode *>s2;
     int temp=9;
        vector<int>data1,data2;
         if(pRoot)//要加这句话
         {
        s1.push(pRoot);
         }
 
        while((!s1.empty())||(!s2.empty()))
        {
           /*if(number==1)
            {
                pRoot=s1.top();
                data1.push_back(pRoot->val);
                s1.pop();
                result.push_back(data1);
               data1.clear();
               number=number+1;
            }*/
            
            if(s1.empty()||(number%2==0&&number!=2))
           
            {
                pRoot=s2.top();
                data2.push_back(pRoot->val);
                    s2.pop();
                if(s2.empty())
                {
                result.push_back(data2);
                    data2.clear();
                
                }
            }
            else 
            {
            pRoot=s1.top();
            data1.push_back(pRoot->val);
            s1.pop();
                if(s1.empty())
                {
                result.push_back(data1);
                  data1.clear();
                }
            }
            if((number%2==0))
            {
              
                if(pRoot->left!=NULL)
                {
                   s1.push(pRoot->left);
                }
               
                if(pRoot->right!=NULL)
               {
                   s1.push(pRoot->right);
               }
                num1++;
       if(num1==pow(2,(number-2)))
       {
            number++;
          num1=0;
           continue;
       }
                else
                {
               continue;
                }
            }
            if(number%2==1)
            {
                   temp=1;
                if(pRoot->right!=NULL)
                {
                    s2.push(pRoot->right);
                }
                if(pRoot->left!=NULL)
                {
                    
                    s2.push(pRoot->left);
                }

        num2++;
            if(num2==pow(2,(number-2)))
                {
                number++;
                num2=0;
                continue;
            }
        else{
            continue;
        }
        } 
       
              }
       return result;
    }
     
    
    
};
