## TCP详解（4）：TCP半连接队列和全连接队列

**什么是半连接全连接队列？**：

半连接队列，也称SYN队列

全连接队列，也称accepet队列

服务端收到客户端发起的SYN请求后，**内核会把该连接存储到半连接队列**，并向客户端响应SYN+ACK，接着客户端会返回ACK，服务端接收到第三次握手的ACK后，**内核会把连接从半连接队列移除，然后创建新的完全的连接，并将其添加到accept队列，等待进程调用accept函数时把连接取出来。**

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211221115351784.png" alt="image-20211221115351784" style="zoom:67%;" />

在服务器使用ss命令，来查看TCP全连接队列的情况：

但需要注意的是ss命令获取的Recv-Q/Send-Q在listen状态下和非listen状态下所表达含义是不同的。

**当超过了TCP最大全连接队列的时候服务器会丢掉后续进来的TCP连接**

从上面可知，当服务器并发处理大量请求时，如果TCP全连接队列过小，那就很容易出现溢出。发生TCP全连接溢出的时候，后续的请求就会被丢弃，这样就会出现服务器端请求数量上不去的现象。

![image-20211221151219330](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211221151219330.png)

实际上，丢弃TCP连接只是Linux的默认行为，我们还可以选择向客户端发送RST复位报文，告诉客户端连接已经建立失败。

tcp_abort_on_overflow共有两个值0和1，分别表示：

0:如果全连接队列满了，那么server扔掉client发过来的ack（默认值）

1：如果全连接队列满了，server发送一个reset包给client，表示废除掉这个握手过程和这个连接

如果要像知道客户端连接不上服务端，是不是服务端TCP全连接队列的原因，可以把tcp_abort_on_overflow设置为1.这时如果在客户端异常状态中可以看到很多connection reset by peer 的错误，那么就可以证明是由于服务端TCP全连接队列溢出的问题。

通常情况下就应该设置为0.因为这样更有利于应对突发流量。

举个列子，当TCP全连接队列满导致服务器丢掉了ACK，与此同时，客户端连接状态确实ESTABLISHED，进程就在建立好的连接上发送请求。只要服务器没有为请求恢复ACK，请求就会被多次重发。如果服务器上的进程只是短暂的繁忙造成accept队列满，那么当TCP全连接队列有空位时候，就会再次收到的请求含有ACK，仍然会成功建立。

所以overflow设置为0才能提高连接建立的成功率，只要你非常肯定TCP全连接队列会长期溢出时，才能设置为1以尽快通知客户端。

### 增大TCP全连接队列：

TCP全连接队列最大值取决于somaxconn和backlog之间的最小值，也就是取Min

somaxconn是Linux内核参数，默认是128 可以设置

backlog是listen(int sockfd , int backlog) 函数中的backlog带下，Nginx默认值是511，可以通过修改配置文件设置其长度；

所以在测试环境的TCP全连接队列最大值是min(128,511)那就是128



## TCP半连接溢出：

TCP半连接队列的长度没有像全连接队列那样可以通过ss命令查看。但是我们可以抓住TCP半连接的特点，就是服务器处于SYN_RECV状态的TCP连接数，就是TCP半连接队列。

要模拟TCP半连接队列溢出场景：

模拟TP半连接溢出场景不难，实际上是对服务器一直发送TCP SYN 包，但是不回第三次握手ACK，这样就会使得服务器有大量的处于SYN_RECV状态的TCP连接。

这其实也是所谓的SYN泛滥 SYN攻击 DDos攻击。

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211221160714669.png" alt="image-20211221160714669" style="zoom:50%;" />

1.如果半连接队列满了，并且没有开启tcp_syncookies则会直接丢弃

2.如果全连接队列满了，并且没有重传SYN+ACK包的连接请求多于1个，则会丢弃；

3.如果没有开启tcp_syncookies，并且max_syn_backlog减去当前连接队列队伍长度小于（max_syn_backlog>>2）,则会丢弃



可见半连接队列最大值不单单由max_syn_backlog决定，还跟somaxconn和backlog有关系

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211221161317190.png" alt="image-20211221161317190" style="zoom:67%;" />

当max_syn_backlog > min(somaxconn.backlog)时候，半连接队列最大值为min(somaxconn.backlog)*2

当max_syn_backlog < min(somaxconn,backlog)时，半连接队列最大值为max_syn_backlog*2



syncookies参数主要有三个值：

​	0 表示关闭该功能

​	1 表示仅当syn半连接队列放不下时启用它

​	2 表示无条件开启该功能

应对syn攻击时候只需要开启1即可



防御SYN攻击的方法：

1.增大半连接队列

2.开启tcp_syncookies功能

3.减少SYN+ACK重传次数

### 方式1增大半连接队列：

想要增大半连接队列，那还需要一同增大全连接队列，因为我们在之前知道半连接队列最大值的取法

### 方式2开启tcp_syn_cookies功能：

开启这个功能也很简单没在Linux中内参参数中修改即可

### 方式3减少SYN+ACK重传次数：

当服务器收到SYN攻击时，就会有大量处于SYN_RECV状态的TCP连接，处于这个状态的TCP会重传SYN+ACK，当重传次数超过上限后，就会断开连接。

那么针对SYN攻击我们就可以减少SYN+ACK重传次数，以加快处于SYN_RECV状态的TCP连接断开。



## TCP性能提升策略：

TCP三次握手的性能提升、TCP四次挥手的性能提升、TCP数据传输的性能提升：

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211221164841364.png" alt="image-20211221164841364" style="zoom:67%;" />

TCP FAST OPEN功能需要客户端和服务端同时支持才有效果

