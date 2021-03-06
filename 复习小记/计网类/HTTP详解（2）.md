## HTTP详解（2）

### Get与Post：

Get方法的含义就是从服务器请求获取资源，这个资源可以是静态的文本、页面、图片等

Post方法则是相反操作，他向URI指定的资源提交数据，数据就放在报文的body里面。

### 安全性： 在HTTP协议里面，所谓的安全是指请求方法不会破坏服务器上的资源。

所谓的幂等，就是指多次执行相同操作，结果都是相同的。

显然，GET方法就是安全且幂等的，因为他只是获取操作，无论获取多少次，服务器上的数据都是安全的且每次结果都是相同的。

而反观Post 是新增或者提交数据的操作，会修改服务器上的资源，所以是不安全的，且多次提交数据就会创建多个资源，所以也是不幂等的。



### HTTP特性：

1.简单。

HTTP基本报文格式就是Header+body，头部信息也是key-value的简单文本形式，易于理解，降低了学习和使用的门槛。

2.灵活和易于扩展。

HTTP协议里面的各类请求方法、URI/URL、状态码、头字段等每个组成要求都没固定死，都允许开发人员自定义和扩充。

同时HTTP由于是在应用层，则它下层可以随意变化，

HTTPS也就是在HTTP于TCP之间新增了SSL/TLS安全传输层，HTTP/3甚至把TCP层换成了基于UDP的QUIC

3、应用官方和跨平台。

互联网发展至今，HTTP的应用范围非常的广泛，从台式机的浏览器到各类APP，HTTP的应用遍地开花，同时天然具有跨平台的优越性



4.HTTP优缺一体： **无状态、铭文传输**

无状态的好处在于服务器不会去记忆HTTP状态所以**不需要额外资源来记录状态信息，减少了服务器负担**，能够把更多内存和CPU用于服务。

无状态的换出在于，既然服务器没有记忆能力，它在完成关联性操作时候会很麻烦（比如密码验证登录导致session的无法使用）

对于无状态问题比较简单的方式就是用COOKIE技术。

cookie通过在请求和响应报文中写入Cookie信息来控制客户端的状态码。

相当于，在客户端第一次请求后，服务器会下发一个装有用户信息的小帖子，后续客户端请求服务器的时候带上小帖子服务器就认识了

明文传输：

明文意味着在传输过程中的信息是可方便阅读的，通过抓包工具都可以肉眼查看的，为我们的调试工作带来了极大的便利性。

但正是这样，HTTP的所有信息都暴露在了光天化日下，相当于信息裸奔，在传输的漫长过程之中内容毫无隐私可言，很容易就会被窃取，如果里面有帐号密码信息，那更是危险。

### 3.不安全：

HTTP比较严重的缺点就是不安全：

1.通信使用铭文

2.不验证对方身份

3.无法证明报文的完整性。

这些问题的解决就是通过HTTPS来解决的，引入SSL/TLS层让其在安全上达到了机制。

### HTTP1.1性能如何？：

HTTP是基于TCP/IP的，并且使用了 请求-应答 的通信模式，所以性能的关键就是在于这两点里。

1.长连接：

早起的HTTP1.0就是每次发送一个请求都要新建一次TCP连接，很麻烦，增加了通信开销。

为了解决上述问题，HTTP1.1就提出了长连接的通信方式也叫做持久连接。这种方式的好处在于减少了TCP连接的重复建立和断开锁造成的额外开销，减轻了服务器的负载。（connection:keep-alive）

持久连接的特点就是，只要任意一端没有明确提出断开连接就保持TCP链接状态。

2.管道网络传输

HTTP1.1采用了长连接的方式，这使得管道网络传输成为了可能。即在同一个TCP链接里面，客户端发起多个请求，只要第一个请求发送高出去了不必等起回来就可以发送第二个请求，就可以减少整体的响应时间。

3.队头阻塞

请求-应答的模式加剧了HTTP性能问题，当顺序发送的请求序列中的一个请求因为某种原因被阻塞时候，在后面排队的所有请求也一同被阻塞了，会招致客户端一直请求不到数据，这也就是队头阻塞

![image-20211123144236191](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211123144236191.png)



![image-20211123144302481](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211123144302481.png)

### HTTPS如何解决上述HTTP的三个问题：

1.混合加密的方式实现了信息的机密性，解决了窃听风险

2.摘要算法的方式来实现完整性，他能够为数据生成独一无二的值，用于校验数据的完整性解决了串改的风险

3.将服务器公钥放在数字证书中解决了冒充的风险。

（HTTPS的加密方式和Token的加密方式有点异曲同工）



## HTTP2相对于HTTP1.1性能上改进：

**1.头部压缩，HTTP/2会压缩头如果你同时发出多个请求，他们的头是一样的或者相似的，那么协议会帮我们去重。**

这就是所谓的HPACK算法在客户端和服务器同时维护一张头信息表，所有字段都会存入这个表，生成一个索引号，以后就不发送这样的字段了，只发送索引号，这样就提高速度了。

**2.二进制格式**

HTTP/2不再像HTTP/1.1里面的纯文本形式报文而是全面采用了二进制格式，**头信息和数据体都是二进制，并统称为帧** （头信息帧和数据帧）。这样接收到报文后无需在将报文转化成二进制增加了数据传输效率。



HTTP/2的数据包不是按顺序发送的，**同一个连接里面连续的数据包，可能属于不同的回应。**因此，必须要对数据包做出标记，指明他属于哪个回应。

**每个请求或回应的所有数据包**，称为一个**数据流**。**每个数据流都标记着一个独一无二的编号**，其中规定用户端发送的数据流编号为奇数，服务器端发送的为偶数。

客户端还可以指定数据流优先级，优先级高的请求，服务器可以优先响应。

![image-20211123145315544](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211123145315544.png)

**4.多路复用**

可以在一个连接中**并发**（并行发送）多个请求或者回应，而不用按照顺序一一对应。

![image-20220214134246377](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220214134246377.png)

**降低了1.1的串行请求不需要排队等待也不会出现队头阻塞问题，降低了延迟，大幅度提高了连接的利用率。**

![image-20220214134254709](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220214134254709.png)

HTTP2对同一域名下所有请求都是基于流，**也就是说同一域名不管访问多少文件，也只建立一路连接。**同样Apache（后端）最大连接数为300，因为这个特效，最大的并发可以提升到300，大幅度提升了传输效率。

在一个TCP连接中，服务器收到了客户端A和B的两个请求，如果发现A处理过程非常耗时，于是就回应A请求已经处理好的部分，接着回应B请求，完成后在回应A请求剩下的部分。

**5.服务器推送**

HTTP2的服务器不仅仅是一再的被动响应，也会主动向客户端推送消息。

## 为什么HTTP2能实现多路复用而HTTP1.1不行：

### 简单来回答就是HTTP2中的多路复用是基于二进制帧来实现的，而在HTTP1.1之中是基于‘文本分割’所以HTTP1.1不行

### HTTP2中的数据是转化成了二进制流，每个数据流会被HTTP2指定成一个二进制帧，这些帧都是拥有独一无二的标志的，所以可以根据标志来确定有哪些包和丢失了哪些包，最后接收端对接收的帧包进行合成就行了，但是HTTP1.1中并没有这种方式，所以无法实现多路复用



### Http2的不足及HTTP3的优化：

#### HTTP2的问题在于，多个HTTP请求复用一个TCP连接，下层的TCP不知道有多少个HTTP请求，所以一旦出现了丢包现象，就会触发TCP重传机制，这样在一个TCP连接中的所有HTTP请求都必须等这个丢了的包重传回来。

基于这个问题，HTTP3把下层的TCP协议改成了UDP。

![image-20211123152504878](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211123152504878.png)

问题在于UDP是不可靠传输，**所以基于UDP的QUIC协议**，可实现类似TCP的可靠传输。

QUIC有一套自己的机制可以保证传输的可靠性。**当某个流发生丢包时候就会阻塞这个流，其他流不会受到影响**。

TLS在HTTP3中变成了1.3版本，头部压缩算法也变成了Qpack

HTTPS要建立一个连接要花费六次交互，先是TLS三次握手，在是TCP的三次握手。QUIC直接把TCP和TLS1.3的六次交互合并成了三次，减少了交互次数。

QUIC是一个在UDP之上的伪TCP+TLS+HTTP2的多路复用协议。

QUIC是新协议，**对于很多网络设备根本不知道什么是QUIC只会当做UDP**，这样出现新问题，所以现在HTTP3的普及很慢。



### 重定向请求次数：

服务器上的一个资源可能由于迁移、维护等原因从url1移至url2后，客户端不知道，但是他还是请求url1这时候服务器不能粗暴的返回错误而是返回302和location头部，通过这样告诉客户端资源已经迁移至url2，此时就需要从星发起HTTP请求至URL2才能连接。

