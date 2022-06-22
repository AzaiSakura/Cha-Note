## TCP三次握手和四次挥手

TCP是双全工通道

传输层作用：就是承上启下的作用，帮助端与端的连接

![image-20211106143659597](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211106143659597.png)

IP地址加上端口号的形式：

IP地址：端口号     这种形式就叫做套接字socket

## 三次握手

![image-20211106144227610](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211106144227610.png)

这就是形象的三次握手了

在使用TCP连接时候就需要三次握手，TCP报文中有SYN ACK这些信息

服务器是不会保存自身序号的，为的是防止大量第二次握手请求攻击。

### 握手之后，这个时候客户端就可以发送HTTP请求了，然后服务器响应内容

## 这个时候可能交流完毕了，要断开连接了，这个时候就是开始的过程就是我们所说的四次挥手了

### 客户端和服务端都能主动发起关闭要求。

![image-20211106144642240](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211106144642240.png)

### 这里为什么要先发一个ACK在追发一个FIN加ACK，原因是此时服务端可能还没有发送完毕的数据

