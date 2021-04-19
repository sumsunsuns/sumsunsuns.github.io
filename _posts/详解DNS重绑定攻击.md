# 理解DNS重绑定攻击



# 0x00 前言

DNS重绑定攻击的用法有很多种，这篇文章主要理解DNS重绑定攻击的原理，并介绍如何通过DNS重绑定来攻击内网设备。为了更好的理解DNS重绑定攻击，我们先从Web浏览器的同源策略开始介绍。

<br>

<br>

**点击查看往期关于DNS文章：**

###### [SAD DNS--新型DNS缓存中毒攻击](https://www.cnblogs.com/PsgQ/p/14071890.html)

###### [DNS 缓存中毒--Kaminsky 攻击复现](https://www.cnblogs.com/PsgQ/p/14601942.html)

<br>

# 0x01 Web浏览器同源策略(SOP)

 **同源策略（Same origin policy）** 是一个重要的安全策略，它用于限制一个[origin](https://developer.mozilla.org/en-US/docs/Glossary/Origin)的文档或者它加载的脚本如何能与另一个源的资源进行交互。它能帮助阻隔恶意文档，减少可能被攻击的媒介。

- **其实换句话来说就是在浏览器中，同一个域名下的网站只能调用本域名下的资源。就好比说现在有一个网址`http://www.psgq.com/index.html`的页面，在同源策略的约束下可以调用`http://www.psgq.com/index2.html`的资源，但是不能调用`http://www.psgq123.com/index.html`的资源（`<script>`,`<link>`,`<iframe>`,`<img>`等标签的SRC属性除外）。同源策略最直接的表现就是能够区分出各个网站的cookie信息，使得我们能够有条不紊的访问各个网站而不会照成信息混乱。**

- **另外，如果我在浏览器同时访问新浪和搜狐两个网站并且这两个网站都在浏览器存储了用户名和密码，那么他们各自只能访问各自存储的用户信息，无法交叉访问，否则就会造成用户信息泄露。**

- 试想这样一种情况：如果没有同源策略，A网站是一家银行，往往用来保存用户的登录状态，如果用户没有退出登录，其他网站就可以冒充用户，为所欲为。因为浏览器同时还 Cookie 可以共享，互联网就毫无安全可言了。

  <br>

**同源的定义：**如果两个 URL 的 协议、域名、端口都相同的话，则这两个 URL 是*同源*。

<br>

举例来说， **`https://www.cnblogs.com/PsgQ/`** 这个网址中， **`https`** 是协议，  www.cnblogs.com是域名，端口是`443`（https协议默认端口可以省略，你也可以把他看成：**`https://www.cnblogs.com:443/PsgQ/`**）。任意两个网址，只要这三点全部相同，那么浏览器就认为它们是同源的，任意一个不相同都会被浏览器认为是跨域。

<br>

###### 注：IE例外

IE浏览器对于同源策略有两个主要的例外：

> 1. 授信范围（Trust Zones）：两个相互之间高度互信的域名，如公司域名（corporate domains），不遵守同源策略的限制。
> 2. 端口：IE浏览器没有将端口号加入到同源策略的组成部分之中，因此 `http://example.com:81/index.html` 和 `http://example.com/index.html`  属于同源并且不受任何限制。

<br>

在浏览器中, `<script> `、`<img>`、`<iframe>`、`<link>`等标签都可以跨域加载,而不受浏览器的同源策略的限制, 这些带src属性的标签每次加载的时候,实际上都是浏览器发起一次GET请求, 不同于普通请求(XMLHTTPRequest)的是,通过src属性加载的资源,浏览器限制了JavaScript的权限,使其不能读写src加载返回的内容

浏览器同源策略中,除了上述的几个标签可以跨域加载外,其他出现跨域请求时,请求会发到跨域的服务器,并且会服务器会返回数据,只不过浏览器"拒收"返回的数据。

<br>

<br>

# 0x02 什么是DNS重绑定攻击？

在网页浏览过程中，用户在地址栏中输入包含域名的网址。浏览器通过DNS服务器将域名解析为IP地址，然后向对应的IP地址请求资源，最后展现给用户。而对于域名所有者，他可以设置域名所对应的IP地址。当用户第一次访问，解析域名获取一个IP地址；然后，域名持有者修改对应的IP地址；用户再次请求该域名，就会获取一个新的IP地址。对于浏览器来说，整个过程访问的都是同一域名，**所以认为是安全的。（浏览器同源策略）**  这就是DNS Rebinding攻击。

<br>

通过DNS重绑定攻击可以绕过同源策略，攻击内网的其他设备。

<br>

**攻击条件：**

>1.攻击者可以控制恶意的DNS服务器，来回复域名查询，如 www.attacker.com
>
>2.攻击者可以欺骗用户在浏览器加载 www.attacker.com。可以通过多种方式，从网络钓鱼到持久性XSS，或[购买HTML横幅广告](https://medium.com/@brannondorsey/browser-as-botnet-or-the-coming-war-on-your-web-browser-be920c4f718)，来实现这一目标。
>
>3.攻击者 控制 www.attacker.com 钓鱼网站的服务器。

<br>

**下面是DNS重绑定攻击的原理图：**

![](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210418141750.png)

<br>

1. 受害者打开钓鱼邮件的链接，他们的Web浏览器会发出DNS查询请求，查询www.attacker.com的IP地址，基本流程：**浏览器缓存 -> 系统缓存 -> 路由器 缓存 -> ISP DNS解析器缓存 -> 根域名服务器 -> 顶级域名服务器 -> 权威域名服务器**，具体解析过程可参考之前关于DNS的介绍文章。

2.  攻击者控制的DNS服务器收到受害者的DNS请求时，会使用www.attacker.com的真实IP地址93.185.216.36进行响应。 它还将响应的[TTL值](https://en.wikipedia.org/wiki/Time_to_live)设置为0秒，以便受害者的机器不会长时间缓存它。

3. 受害者的浏览器向钓鱼网站服务器发出HTTP请求加载网页。

4. 攻击者进行HTTP响应，并通过JS加载一些恶意代码，该页面反复向www.attacker.com 发出POST请求。（POST请求包含恶意代码）

   <br>

![原理图](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210418141826.png)

<br>

5.因为之前DNS  ttl设置为0，缓存很快就失效，所以浏览器继续向恶意权威域名服务器发出查询。

6.此时，恶意DNS服务器收到查询后，回复一个内网设备的IP（也可以是一些物联网设备），如192.168.0.5。

7.因为满足同源策略，浏览器认为是安全的，于是向内网其他设备发送POST恶意请求。

<br>

**为什么绕过了同源策略？**

因为同源策略浏览器看的是协议，域名，端口，这些并没有变化，而背后的IP地址却已经变了，所以造成了跨域的请求。

<br>

**TTL是什么？**

> TTL是英语Time-To-Live的简称，意思为一条域名解析记录在DNS服务器中的存留时间。当各地的DNS服务器接受到解析请求时，就会向域名指定的NS服务器发出解析请求从而获得解析记录；在获得这个记录之后，记录会在DNS服务器中保存一段时间，这段时间内如果再接到这个域名的解析请求，DNS服务器将不再向NS服务器发出请求，而是直接返回刚才获得的记录；而这个记录在DNS服务器上保留的时间，就是TTL值。

它表示DNS记录在DNS服务器上缓存的时间,数值越小,修改记录各地生效的时间越快。

<br>

<br>

# 0x03 总结

本篇文章详细介绍了Web浏览器的同源策略，以及DNS重绑定攻击内网设备的原理，后续会为大家继续介绍关于DNS重绑定攻击的其它用法。

<br>



###### 有关DNS的详细介绍，可参考之前DNS专栏下的文章。

<br>

<br>

# 0x04 参考资料

[Web安全(一)---浏览器同源策略](https://blog.csdn.net/coxhuang/article/details/105040363)

[Web安全(二)---跨域资源共享](https://blog.csdn.net/Coxhuang/article/details/105040366)

[【WEB】浏览器同源策略及跨域资源共享(CORS)](https://www.jianshu.com/p/9e46bc046908)

[浏览器的同源策略](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)

[利用DNS重绑定攻击专用网络](https://zhuanlan.zhihu.com/p/45583472)

[利用DNS缓存和TLS协议将受限SSRF变为通用SSRF](https://xz.aliyun.com/t/8351#toc-6)



















