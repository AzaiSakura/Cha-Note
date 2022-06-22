## Websocket

Websocket是一种基于TCP的新的网络协议，它实现了浏览器与服务器全双工通信，允许服务器主动发送信息给客户端

![image-20220118211155114](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220118211155114.png)

传统的AJAX轮询，也能完成需求，但是性能低的可想而知



## H5中新增的WebsocketApi：

new Websocket（） 构造函数

socket.open()建立连接

socket.close()关闭连接

socket.send()发送连接



![image-20220122113523994](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220122113523994.png)

## socket框架 ： socket.io

socket.io不是websocket的实现，只是一个接口，只有一套api 前后端都可以用

