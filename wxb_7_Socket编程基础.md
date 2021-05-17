---
title: '7 Socket编程基础'
date: 2021-01-26 18:00:00
tags: [计算机,操作系统,Linux,网络系统,海贼班]
published: true
hideInList: true
feature: 
isTop: false
---
# 课程内容

>套接字是什么？网络编程是做什么的？
* 了解TCP/IP五层模型、OSI七层模型
* 类比
    * 套接字——快递员
    * 运输层——快递公司：TCP——某丰快递公司，UDP——某通快递公司
    * 交通运输道路——因特网
    * 通讯地址——IP
## ——运输层协议——

>类比快递公司
>对于开发者，只能选择TCP或UDP协议，修改协议参数
## TCP

传输控制协议；面向连接，可靠的数据传输协议

* 连接：三次握手 [详见**附加知识点**]
* 可靠的本质：确认与重传 [需要有序号]
    * 如果丢了就会重传
    * [PS] 双方都保存一些描述双方状态的变量
* **头部格式**
* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/22zEwoOlCHrh5aVl.png" alt="图片" style="zoom: 80%;" />
* 源**端口**号：从哪个端口发出；目标端口号：发送到哪个端口
    * 不同的端口对应不同的应用
    * 如果把计算机比作一个大楼，端口号就是大楼里的房间号
    * IP地址由IP层给出
* **序列号**：标志第几次通信；确认应答号：期望对方下次通信的序列号
* 首部长度：单位为字 [一般为4字节]
* **功能比特位**[关注标黄处]
    * ACK：确认
    * RST：重置连接 [拒绝下次连接]
    * SYN：建立请求 [三次握手中前两次握手会用到]
    * FIN：关闭连接 [四次挥手的第一、第三次挥手会用到，同时还可以放一些数据，详见**附加知识点**]
* 窗口大小：告诉对方还可以发多少数据，用来抑制对方的发送速率
* 校验和：确认数据是否正确。如果有问题，直接销毁，然后请求重发
* [PS]
    * 设计了这么多主要为了可靠
    * 现实中的快递公司无法达到可靠，因为运输的物品是唯一的
## UDP

用户数据报协议；无连接，不可靠的数据传输协议

* 无连接：不需要握手
* 不可靠：不管对方是否收到
* 优势：灵活、成本低
* **头部格式**
* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/zxSXY37hhiV9djBF.png" alt="图片" style="zoom:67%;" />
* 相对TCP，简单得多
## ——socket——

>类比快递员，但只为一个任务服务
>进程和运输层之间的接口，进程发送网络数据必须经它交给运输层去交付
## 【生与死】

## socket：创建套接字

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/kRgUvWdxxv9EE0c7.png" alt="图片" style="zoom:67%;" />
* domain：域名类型
    * AF_INET，对应ipv4 [常用]
    * AF_INET6，对应ipv6
* type：类型
    * SOCK_STREAM，对应字节流 [TCP]
    * SOCK_DGRAM，对应数据报 [UDP]
* protocol：协议
    * domain和type可能唯一确定protocol，如AF_INET与SOCK_STREAM确定IPPROTO_TCP
    * [PS] 只能选一个的话，可用**0**代替
* 返回值：文件描述符
    * 出错则返回-1
    * socket也是一个文件，一切皆文件
## close：关闭连接

* int close(int fd);
* 四次挥手 [详见附加知识点]
* 两端都需要调用close，调用方发送FIN，接收端的recv的**返回值为0**
## 【服】

## bind：绑定IP和端口

只针对于收数据方

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/OgkIlfIyliTM5eW5.png" alt="图片" style="zoom:67%;" />
* sockfd：文件描述符
* addr：IP地址和端口
    * 绑定IP：可以接收来自该IP地址的数据 [本机]
        * 若为空，则可以接收来自任意IP地址的数据
        * 可用在内网和外网的交接处，作为防火墙使用
    * 绑定端口：服务于哪个端口 [共$2^{16}=65536$个端口]
* addrlen：地址长度
* 返回值：成功，0；否则，-1
### **+相关结构体：sockaddr、sockaddr_in**

**sockaddr**

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/UUA1L3vmzQhFAfki.png" alt="图片" style="zoom:67%;" />
* sin_family：地址协议族，一般使用AF_INET，对应ipv4
* sa_data：同时包含IP地址和端口
* ❗ 使用并不方便，转用下列更友好的方式👇，再使用(struct sockaddr*)强转即可

**sockaddr_in**

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/V3Nr8MGVDCk71SNU.png" alt="图片" style="zoom:67%;" />
* sin_port：**端口**号 [需要**网络字节序**，见下]
* sin_addr：**IP**地址
* 其中，sin_addr对应一个新的结构体**in_addr**
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/Jk7gr6uhcOkNI7c6.png" alt="图片" style="zoom:67%;" />
    * 存储32位无符号整型，一般使用**inet_addr**函数将**点分十进制**转换为in_addr结构体：
        * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/hW1wGoM0IJ2wRc8N.png" alt="图片" style="zoom:67%;" />
        * **点分十进制**表示 [字符串形式] 更方便
        * inet_ntoa，则反之
### **+主机字节序 & 网络字节序**

* 主机字节序：大端、小端
    * 常见为**小端**机，**低**位字节排放在内存的**低**地址端
* 网络字节序：对于4个字节的32bit值，先传输0\~7bit，...，最后传输24\~31bit
* 整形字节序的转换函数
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/fiOwuV68C3WKBPZM.png" alt="图片" style="zoom:67%;" />
    * htonl：32位主机字节序到网络字节序的转换
    * htons：16位主机字节序到网络字节序的转换
    * ntohl、ntohs，则反之
## litsen：设为监听态

将套接字从主动（默认）切换为被动 [首先需要bind绑定端口]

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/GBLxx7faWM6SHN3M.png" alt="图片" style="zoom:67%;" />
* 注意：第二个参数的真实含义是完成队列的长度
    * ① TCP连接过程存在两个队列
        * **未完成队列**：客户端发送SYN过来，服务器回应SYN+ACK之后，服务器当前处于SYN_RECV状态，此时的连接处在未完成队列中
        * **完成队列**：客户端回应ACK之后，两边都处于ESTABLISHED状态，此时连接从未完成队列**转移**到完成队列中
        * 👉 当服务器调用accept时，才将连接从完成队列中移除
    * ② 注意事项：设置合适的backlog；服务端要尽快accept新的连接
## accept：接受连接

生成一个新的快递员 [还可继续建立多个连接]

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/N3V0XORx7JISzLOB.png" alt="图片" style="zoom:67%;" />
* ① 传入的sockfd必须经过socket()、bind()、listen()处理
* ② addr为传出参数，用来存储客户端地址
* 返回值
    * 成功，则返回一个**新的sockfd**，原先的sockfd仍可用来accept
    * 失败，则返回-1
* [PS] 一般新的scokfd使用完毕就将其关闭；listen状态的socket不关闭
## 【客】

## connect：建立连接

主动套接字，最多只能连**一个**

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/GJvc9u55zGHvTS2s.png" alt="图片" style="zoom:67%;" />
* 与accept不同的是：
    * sockfd不需要经过bind()、listen()处理
    * 不会返回新的socket

⭐ connect和accept是一对，分别在客户端和服务端执行，在此期间，完成了三次握手

## 【传输】

## send：发送数据

本质同[write](https://doublelll3.ml/wxb_1_%E6%96%87%E4%BB%B6%E3%80%81%E7%9B%AE%E5%BD%95%E6%93%8D%E4%BD%9C%E4%B8%8E%E5%AE%9E%E7%8E%B0ls%E7%9A%84%E6%80%9D%E8%B7%AF/#write?fileGuid=zdkyBEa86pCYmlA6)

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/l46q5pF4fGtwVpgu.png" alt="图片" style="zoom:67%;" />
* ❗ sendto多传入了dest_addr和addrlen，它是用于UDP的
    * 因为没有建立连接，从而需要指定目的IP和端口
* flag一般置为0
## recv：接收数据

本质同[read](https://doublelll3.ml/wxb_1_%E6%96%87%E4%BB%B6%E3%80%81%E7%9B%AE%E5%BD%95%E6%93%8D%E4%BD%9C%E4%B8%8E%E5%AE%9E%E7%8E%B0ls%E7%9A%84%E6%80%9D%E8%B7%AF/#read?fileGuid=zdkyBEa86pCYmlA6)

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/pZPxTWro2jZi6d72.png" alt="图片" style="zoom:67%;" />
* 当对方断开时，返回值为0
* ❗ recvfrom多传入了src_addr和addrlen，它是用于UDP的
    * src_addr存储发送数据端的地址信息
* 默认是阻塞的
## ——附加——

## kill

给一个进程发送信号

* man 2 kill
* 原型
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/0GCOYMAiRNUEDPWc.png)
    * 基于进程ID和信号位掩码
* 描述
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/pKhjaMSIbHG2g8pT.png)
    * 设置pid有各种形式
    * 均需要存在和权限检查
* 返回值
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/Q6gnAVTARsJZRXdu.png)
    * 0，成功；-1，出错
* kill -l 查看信号列表
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/Mmlbp8rlT9B7NLN6.png" alt="图片" style="zoom:67%;" />
    * 64种信号
## signal

信号的处理方式

* man signal
* 原型
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/Mz8QFutg5aKAj8Oo.png)
    * 需要定义一个sighandler_t类型的函数
* 描述
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/gvIS3OTHiNDdGPwv.png)
    * 其行为会随UNIX版本变化
    * handler有三种类型：忽略、默认、自定义
    * 自定义类型涉及涉及捕鼠器原理：夹住一个老鼠的时候，后面一个老鼠可能被丢失
        * 需要重新设置 [由系统操作]
* 返回值
    * ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/jyPDaV7gb3o8IM7a.png)
    * 根据handler而定
# 代码演示

## 服务端

tcp_server.h

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/e9PzcWwmUxaZwExQ.png" alt="图片" style="zoom:67%;" />
* 在指定端口上创建一个处于监听状态的快递员

tcp_server.c

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/tHVDLd7f4YqTJtWj.png" alt="图片" style="zoom:67%;" />
* 参照序号阅读
* 注意：head.h中添加socket相关的头文件，可在man手册中查找，这里不赘述

1.server.c

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/dcbFehW6bnDB9Xeb.png" alt="图片" style="zoom:67%;" />
* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/Jk5Q7puuOBzag29S.png" alt="图片" style="zoom:67%;" />
* accept可以获取客户端的地址信息
* 创建子进程单独用于传输数据
* 每一步都要注意有错误检测
    * +断开连接情况（FIN，recv返回为0）的处理
* 收发策略不同
    * 有多少发多少，能收多少收多少
    * send使用strlen，recv使用sizeof
## 客户端

tcp_client.h

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/F5zmgYqw9m3sxIdx.png" alt="图片" style="zoom:67%;" />
* 主动连接指定IP [点分十进制的ipv4字符串] 和端口

tcp_client.c

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/9da6ArxMgIeuyusI.png" alt="图片" style="zoom:67%;" />
* 填表基于输入

1.client.c

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/5UwmCtAod03u0KUA.png" alt="图片" style="zoom:67%;" />
* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/qHbXoss0yugR5nKI.png" alt="图片" style="zoom: 67%;" />
* 加入了对信号的捕捉
* bzero的使用，初始化buff变量
## 效果展示

* ![图片](https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/JhrdLiS2L5fq6pm5.png)
* 左：服务端，右：客户端 [可多用户]
* 建立连接、地址捕获、数据传输、断开连接
* 使用netstat可查看端口的监听状态
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/A0t7d4Aq70Q0Glqm.png" alt="图片" style="zoom:67%;" />
    * 添加-alnt选项
* [PS] 需要在云主机的控制台——安全组中开放端口8888
# 附加知识点

* IP：公共的地址服务，尽力而为交付服务。另一层含义，其不可靠 [有可能出车祸]
## 三次握手、四次挥手

* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/RVvwDJVpHvVYieL3.png" alt="图片" style="zoom: 33%;" />
* 三次握手 [SYN、ACK]
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/C51Xz7NYuU8B5eIK.png" alt="图片" style="zoom:67%;" />
    * **第一次握手**：客户端发送SYN包到服务器[客户端进入SYN_SEND状态，等待服务器确认]
    * **第二次握手**：服务器收到，必须确认客户端，设置一个ACK，同时自己也设置一个SYN，即SYN+ACK包[服务端从LISTEN进入SYN_RECV状态]
    * **第三次握手**：客户端收到服务器的SYN＋ACK包，向服务器发送ACK确认包，发送完毕后，客户端进入ESTABLISHED状态，服务器收到ACK后也进入ESTABLISHED状态
    * 注意：每次的ACK序号，在需要确认的包的序号上加一，表示确认
* 四次挥手 [FIN、ACK]
    * <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/GlWgLx87BeFdH15F.png" alt="图片" style="zoom:67%;" />
    * **第一次挥手**：假设客户端想要关闭连接，客户端发送一个FIN包，表示自己已经没有数据可以发送了 [此时仍然可以接收数据][客户端进入FIN_WAIT_1状态]
    * **第二次挥手**：服务端回复一个ACK包，表明自己接收到了客户端关闭连接的请求，但自己还需要做些准备来关闭连接[服务端进入CLOSE_WAIT状态]
        * 客户端接收到这个ACK后，进入FIN_WAIT_2状态，等待服务端关闭连接
    * **第三次挥手**：服务端准备好关闭连接时，向客户端再发送FIN[服务端进入LAST_ACK状态，等待客户端的确认]
    * **第四次挥手**：客户端接收到来自服务器端的关闭请求，发送一个ACK包[客户端进入 TIME_WAIT状态，为可能出现的超时重传的FIN包，等待**2个MSL**时间]
        * 服务端接收到这个ACK之后，关闭连接，进入CLOSED状态
        * 客户端等待了**2个MSL**后，如果没有收到服务端的FIN，则认为服务端已经正常关闭连接，于是自己也关闭连接，进入CLOSED状态；否则，再次发送ACK
* 参考[三次握手与四次挥手](https://hit-alibaba.github.io/interview/basic/network/TCP.html#%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B%E4%B8%8E%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B?fileGuid=zdkyBEa86pCYmlA6)——博客 [注：第四次挥手客户端等待的是超时重传的FIN而不是ACK]
### 附加：2个MSL的含义

TIME_WAIT是如何引起的，有什么作用，在编程时有什么弊端，怎么解决？

* 引起原因：TCP的四次挥手时，已经完成前三次挥手，在第四次挥手时，客户端收到来自服务端的FIN，它在发送一个ACK后，就会进入TIME_WAIT状态
    * 此时客户端需要等待两个最大**数据段生命周期**（Maximum segment lifetime，MSL）的时间之后，才会进入CLOSED状态
* 存在原因
    * ①**阻止延迟数据段**
        * 每一个TCP数据段都包含唯一的序列号，这个序列号能够保证TCP协议的可靠
        * 为了保证新TCP连接的数据段不会与还在网络中传输的历史连接的数据段重复，TCP连接在分配新的序列号之前需要至少**静默**数据段在网络中能够存活的最长时间，即MSL
        * 从而防止延迟的数据段被其他使用相同源地址、源端口、目的地址以及目的端口的TCP连接收到
    * ②**保证连接关闭**
        * 如果客户端等待的时间不够长，当服务端还没有收到ACK消息，而客户端重新与服务端建立TCP连接时，会发生：
            * 服务端因为没有收到ACK消息，所以仍然认为当前连接是合法的
            * 客户端重新发送SYN消息请求握手时，会收到服务端的RST消息，连接建立的过程被终止
        * 所以要保证TCP连接的远程被正确关闭，即等待被动关闭连接的一方收到FIN对应的ACK消息
* 编程影响
    * 对于高并发的场景容易出现过多的TIME_WAIT
    * 而MSL的时长一般是60s，这是难以接受的，可能一个TCP连接只为了通信几秒钟，但TIME_WAIT就需要等待2分钟
* 解决方式
    * 基于一个**时间戳变量**，记录发送数据包、最近一次接收数据包的时间
    * 然后配合两个参数
        * **reuse**：允许主动关闭连接的一方，再次向对方发起连接的时候，复用处于TIME_WAIT状态的连接
        * **recycle**：内核会快速回收处于TIME_WAIT的连接，只需等待RTO时间 [数据包重传的超时时间]
* 参考
    * [为什么 TCP 协议有 TIME_WAIT 状态](https://draveness.me/whys-the-design-tcp-time-wait/?fileGuid=zdkyBEa86pCYmlA6)——博客
    * [系统调优你所不知道的TIME_WAIT和CLOSE_WAIT](https://zhuanlan.zhihu.com/p/40013724?fileGuid=zdkyBEa86pCYmlA6)——知乎
    * [理解TIME_WAIT，彻底弄清解决TCP: time wait bucket table overflow](https://www.yisu.com/zixun/63792.html?fileGuid=zdkyBEa86pCYmlA6)——亿速云
## C语言下的socket编程

* 服务端：socket、sockaddr[_in]、bind、listen；accept、send/recv；close
* 客户端：socket、sockaddr[_in]、connect；send/recv；close
* <img src="https://cdn.jsdelivr.net/gh/doubleLLL3/blogImgs@main/img/EK9MDAKZoTqQbhUY.png" alt="图片" style="zoom:67%;" />
* 基于TCP的流式套接字、基于UDP的数据报套接字
    * UDP服务端也需要bind IP与端口，但不需要listen，使用sendto、recvfrom来发、收信息
* sockaddr[_in]：保存socket信息的结构体，使用[_in]填写信息，再转换为sockaddr
* 服务端需要两个套接字，一个用来监听，一个用来接收客户端connect发送的套接字
## 输入kaikeba.com并按下回车

->到TCP建立连接，本地发送第一个request报文->到收到第一个request报文为止，发生了哪些事情？

* [宏观层面] DNS👉TCP连接 [应用层、传输层、网络层、数据链路层]👉服务器处理请求👉返回响应结果
* DNS
    * 本地hosts、本地DNS解析器缓存
    * 本地DNS
    * 迭代/递归：根DNS服务器，顶级DNS，权威DNS
    * 直到找到域名对应的IP
* TCP连接
    * 应用层：发送HTTP请求——请求方法、URL、HTTP版本
    * 传输层：与服务器进行三次握手
    * 网路层：ARP协议查询IP对应的MAC地址，如果在一个局域网内，就直接根据MAC地址发送请求；否则使用路由表，查找下一个跳转的地址，再访问对应的MAC地址
    * 数据链路层：以太网协议
    * 广播：向一个局域网中的所有机器发送请求，比较MAC地址
* Web服务器
    * 解析用户请求，知道了需要调度哪些资源文件，并调用数据库信息，返回给浏览器客户端
* 返回响应结果
    * 一般会有一个HTTP状态码，比如200、301、404等，通过这个状态码我们可以直到服务器端的处理是否正常，并能了解具体的错误
* ⭐ 推荐视频：[TCP-IP Explained (2000)](https://www.youtube.com/watch?v=7rR8p6gsExY&fileGuid=zdkyBEa86pCYmlA6)——Youtube
    * [主要从IP层展开]
    * 涉及对象：TCP包、ICMP Ping包、UDP包、死亡之Ping、路由器、路由器交换机...
    * 大致流程
        * **本地**：封装包、本地传输、本地路由器选择、交换机选择、代理检查、防火墙检查、本地传输、路由器选择
        * ——>网络传输——>
        * **响应端**：防火墙检查 [监管端口]、代理检查请求包、返回相应信息给请求端、同上述本地过程 [封装包、...、路由器选择]
## 端口复用相关

一个端口可以同时绑定不同的服务吗？

* 可以。接收数据时，根据五元组\{**传输协议，源IP，源端口，目的IP，目的端口**\}判断数据属性
* 例如：
    * 使用TCP和UDP传输协议监听同一个端口后，接收数据互不影响，不冲突
    * 同样，accept产生新的socket，还是用的同一个端口
        * 产生了多个不同的socket，这些socket里包含的目的IP和端口是不变的，变化的只是源IP和端口 [端口复用]
* [PS] TCP类型socket只给TCP类型发数据
## 父子进程的socket关系

父进程克隆出的子进程里的socket和父进程的socket的关系

* 是同一个，对应同一个文件
* 当有数据到来时，两个进程谁先收到数据则谁有该数据，另一个进程继续等待
* 所以一般地，子进程不需要的资源就不要继承，如：可使用close直接关闭子进程中继承自父进程的socket
# Tips

* 系统/网络编程要考虑所有可能出错的地方
* 信号知识扩展：实现自己的sleep函数
* 编译时要记得考虑所有相关的源文件 [*.c]

---


