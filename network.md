### OSI七层网络
1. 物理层 
2. 链路层
3. 网络层 
   1. IP
   2. ICMP
   3. ARP  根据IP地址获取物理地址的一个TCP/IP协议
   4. RARP 
4. 传输层
   1. TCP
   2. UDP
5. 会话层
   1. DNS
6. 表示层
7. 应用层
   1. HTTP
   2. FTP

### 三次握手过程

​		![三次握手](E:/interview/images/network/three.png)



#### 	为什么不是两次？

 防止已经失效的连接请求报文突然又传送到了服务器，从而产生错误。

### 四次挥手过程

![四次挥手](E:/interview/images/network/four.png)

#### **Time_wait和Close_wait**的区别？

TIME_WAIT 是主动关闭链接时形成的，等待2MSL时间，约4分钟。主要是防止最后一个ACK丢失。

CLOSE_WAIT是被动关闭连接是形成的。
