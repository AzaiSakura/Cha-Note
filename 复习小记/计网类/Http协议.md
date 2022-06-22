# Http（超文本传输协议）（应用层的协议）

## http协议：

我们上网就需要网上的资源，就需要请求报文和响应报文。

![image-20211107114248914](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211107114248914.png)

### 一般来说页面表单验证都是用的post方法，因为要返回响应数据。

### 状态码：

![image-20211107114411109](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211107114411109.png)



### 传输层协议（TCP UDP）  网络层协议（IP）



### HTTP1.1中默认连接为持久连接，客户端可以持续向服务器发出请求，如果没有需要发送的了客户端发送Connection:close首部来告诉服务器进行关闭连接



## Http和Https的区别：

http是一种用明文的方式传输我们的内容没有任何加密，为了防止我们的重要信息被截取，这个时候就加入了一个SSL协议，这个协议就是提供了一个数据安全和完整性的协议，负责网络连接的加密，这就是Https协议

### 为了保证用户登录功能，如果是保持HTTP的无状态的话，那用户登录了一次第二次访问又要登录，因此cookie就出现了。

需要在首部字段加上cookie这样就可以实现保持登录状态从而实现状态管理

### 简单了解HTTPS的机制：

我们的设备和服务器开始连接的时候会先等服务器发送证书信息来确定我们访问的服务器就是我们的目标服务器。

![image-20211106171300023](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211106171300023.png)



![image-20211106171439167](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211106171439167.png)



### SSL是TLS的前身 目前大部分浏览器都不支持SSL而是支持TLS

