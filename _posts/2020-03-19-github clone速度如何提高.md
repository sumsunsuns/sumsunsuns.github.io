---
layout:     post
title:      提高GitHub clone速度
subtitle:   
date:       2020-03-19
author:     SGQ
header-img: img/post-sgq-20200319.jpg
catalog: true
tags:
    - GitHub
    - 
    - 
    - 
---

## 前言
 
  感谢学姐的提醒，哈哈，下了GitHub Desktop，果然管理博客变得很轻松。但clone到本地却遇到了一些困难，之前网上搜了好多办法，均未成功，最后一次的尝试，速度由1k/s ~ 10k/s直接飙到3M/s ~ 8M/s。记录下来，以备后用！
  
## 具体过程：

**1.Win+X 打开 Windows PowerShell（管理员) **

 **2.输入notepad，打开记事本。然后用记事本打开C:\WINDOWS\system32\drivers\etc下的hosts。**
 
 **3.将下述IP地址与网站的解析复制到hosts的最后。**
 

``` javascript
# GitHub 
151.101.112.249 http://global-ssl.fastly.Net 
152.192.30.253.112 http://github.com 
153.151.101.44.249 github.global.ssl.fastly.net 
154.192.30.253.113 github.com 
155.103.245.222.133 assets-cdn.github.com 
156.23.235.47.133 assets-cdn.github.com 
157.203.208.39.104 assets-cdn.github.com 
158.204.232.175.78 documentcloud.github.com 204.232.175.94 gist.github.com 
159.107.21.116.220 help.github.com 
160.207.97.227.252 nodeload.github.com 
161.199.27.76.130 raw.github.com 
162.107.22.3.110 status.github.com 
163.204.232.175.78 training.github.com 2
164.07.97.227.243 www.github.com 
165.185.31.16.184 github.global.ssl.fastly.net 
166.185.31.18.133 avatars0.githubusercontent.com 185.31.19.133 avatars1.githubusercontent.com

```







** 最后别忘了刷新dns，cmd下输入：
`ipconfig /flushdns 





***

## 参考资料：
[解决GitHub Clone 速度过慢问题](https://blog.csdn.net/qq_22067469/article/details/86533130?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)



2020年03月20日 20时49分20秒













