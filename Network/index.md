# 计算机网络学习笔记

- [计算机网络学习笔记](#计算机网络学习笔记)
  - [HTTP协议：](#http协议)
  - [TCP/IP协议簇](#tcpip协议簇)
  - [ARP协议 网络层协议](#arp协议-网络层协议)
  - [正向代理与反向代理](#正向代理与反向代理)
  - [web服务器与web框架以及web应用的区别](#web服务器与web框架以及web应用的区别)
  - [Nginx简单操作](#nginx简单操作)
  - [密钥加密过程](#密钥加密过程)

## HTTP协议：
*HTTP(hyper text transfer protocol)* **超文本传输协议**，是网络服务端与客户端之间用于传输 **超文本(语音、视频、超链接)** 的一种协议。通过对于不同 *request* 请求，响应不同的传输内容。

- *status code*:
  - 200: OK,表示 *request* 请求正常被接受。
  - 300: 永久移动。 此响应代码表示所请求资源的URL已永久更改。
  - 401: 未认证。
  - 403: 被禁止访问。
  - 404: NOT FOUND。
  - 500: 内部服务器错误。服务器遇到了它不知道如何处理的情况。

## TCP/IP协议簇
*TCP/IP(Transmission Control Protocol/Internet Protocol)* 是指能够在多个不同网络间实现信息传输的协议簇。*TCP/IP协议* 不仅仅指的是 *TCP* 和 *IP* 两个协议，而是指一个由 *FTP、SMTP、TCP、UDP、IP* 等协议构成的协议簇，同时是Internet最基本的协议、Internet国际互联网络的基础，由网络层的 *IP协议* 和传输层的 *TCP协议* 组成。*TCP/IP* 定义了电子设备如何连入因特网，以及数据如何在它们之间传输的标准。

- TCP  
  - **三次握手**  
  1、第一次握手：Client 将标志位 *SYN* 置为1，随机产生一个值 `seq=J`，并将该数据包发送给 Server，Client进入 *SYN_SENT* 状态，等待Server确认。  
  2、第二次握手：Server 收到数据包后由标志位 `SYN=1` 知道 Client 请求建立连接， Server 将标志位 *SYN* 和 *ACK* 都置为1，`ack=J+1`，随机产生一个值 `seq=K` ，并将该数据包发送给 Client 以确认连接请求，Server 进入 *SYN_RCVD* 状态。  
  3、第三次握手：Client收到确认后，检查*ack* 是否为`J+1`，*ACK* 是否为1，如果正确则将标志位ACK置为1，`ack=K+1`，并将该数据包发送给Server，Server检查 *ack* 是否为`K+1`，*ACK* 是否为1，如果正确则连接建立成功，Client和Server进入 *ESTABLISHED* 状态，完成三次握手，随后Client与Server之间可以开始传输数据了。

  三次握手保证了服务端与客户端双方都确认了通信的建立，可以类比网络语音。

  - **四次挥手**  
  1、第一次挥手，客户端设置 *seq* 和 *ACK* ,向服务器发送一个 FIN(终结)报文段。此时，客户端进入 *FIN_WAIT_1* 状态，表示客户端没有数据要发送给服务端了。  
  2、第二次挥手，服务端收到了客户端发送的 *FIN* 报文段，向客户端回了一个 *ACK* 报文段。  
  3、第三次挥手，服务端向客户端发送 *FIN* 报文段，请求关闭连接，同时服务端进入 *LAST_ACK* 状态。  
  4、第四次挥手，客户端收到服务端发送的 *FIN* 报文段后，向服务端发送 *ACK* 报文段,然后客户端进入 *TIME_WAIT* 状态。服务端收到客户端的 *ACK* 报文段以后，就关闭连接。此时，客户端等待 *2MSL*（指一个片段在网络中最大的存活时间）后依然没有收到回复，则说明服务端已经正常关闭，这样客户端就可以关闭连接了。
> [Java汤姆](https://zhuanlan.zhihu.com/p/128000072)  

- **TCP header**  
  如图所示，数据在不同层级名字有所不同，同一个数据包在应用层、传输层、网络层、链接层的名称分别为：包(`Packet`)、段(`Segment`)、包(`Packet`)、帧(`Frame`)。有时为了方便，可统称为数据包。
  
  ![TCPheader](../images/TCPheader.JPG)
  
  **TCP header** 具体结构如下图所示 
  
  > [MrPeak杂货铺](https://zhuanlan.zhihu.com/p/25766448)
  
  ![TCPheader_2](../images/TCPheader_2.JPG)
  
  一个**TCP header**一般含有20个字节，分5行，每行4个字节。第一行记录发送端端口号以及接收端端口号，每个端口号各占两个字节，即TCP一共可以使用 `2^16 = 65536`个端口。第二、三行的*Sequence Number*和*ACK number*以及第四行的各种Flag用于建立和断开TCP连接。

## ARP协议 网络层协议
- ARP(address resolution protocol)地址解析协议：根据目的IP地址请求目的MAC地址，完成由IP地址至MAC地址的映射。
- ARP使用过程：
  - 检查ARP高速缓存，如果缓存内没有对应IP地址表项，则封装目的MAC地址为FF-FF-FF-FF-FF-FF的帧并在局域网内广播。目的主机收到请求后就会向源主机单播一个ARP响应分组。源主机收到后写入ARP缓存（10-20 min更新一次）

## 正向代理与反向代理
- **正向代理**：指代理对象为客户端，服务端不知道真实的客户端是谁。(如[向马云借钱](https://www.zhihu.com/question/24723688))是通常意义上的代理。
- **反向代理**：指代理对象为服务端，客户端不清楚具体提供服务的服务器地址。(如拨打10086)

## web服务器与web框架以及web应用的区别
- **web服务器**：一般用于接受浏览器发送的 *HTTP request* 并返回相应 *response*。主要功能在于维护客户端与服务端信息通道，主要挑战在于大量数据的同时并发处理。
- **web框架**：主要用于开发**web应用**，实现业务逻辑。
- **web应用**：根据**web服务器**接受的请求，确定要返回的内容，并将其传给**web服务器**

## Nginx简单操作

- 启动：

  - 直接使用`nginx`命令
  - 使用`service nginx start`将`nginx`当做后台服务运行

- 控制：

  - `nginx -s signal` 其中`signal`可以为以下四种

    - `stop`:fast shutdown
    - `quit`:graceful shutdown
    - `reload`:reloading the configuration file
    - `reopen`:reopening the log files

    注意：`nginx`支持配置文件的在线更改，使用`nginx -s reload`即可直接切换至新配置，不同中断已有服务。

- 配置：

  - `nginx`配置文件位于`/etc/nginx/nginx.conf`，其格式以及使用参考[官方文档](http://nginx.org/en/docs/beginners_guide.html)

## 密钥加密过程
>[fucking-algorithm](https://github.com/labuladong/fucking-algorithm/blob/master/%E6%8A%80%E6%9C%AF/%E5%AF%86%E7%A0%81%E6%8A%80%E6%9C%AF.md)  

  ![TCPheader_2](../images/非对称密钥加密过程.jpg)
>>>>>>> c79716525313ab3e8985c9d2d3758e9078ed2586
