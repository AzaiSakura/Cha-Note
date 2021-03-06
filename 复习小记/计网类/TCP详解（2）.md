# TCP详解（2）

## TCP重传、滑动窗口、流量控制、拥塞控制、

![image-20211216110437849](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211216110437849.png)



## TCP重传：

TCP实现可靠传输的方式之一，是通过序列号和确认应答，

在TCP中，当发送端的数据到达主机时，接受端主机会返回一个确认应答ACK表示已经收到消息

但是在错综复杂的网络，并不一定能如愿那么顺利的正常数据传输，万一数据在传输过程中丢失了呢。

所以TCP针对数据包丢失的情况，会用重传机制解决。

常见的重传机制：1.超时重传 2.快速重传    3. SACK 4.D-SACK

### 超时重传：

重传机制的其中一个方式就是在发送数据时，设定一个定时器，当1超过指定的时间后，没有收到对方的ACK确认应答报文，就会重发该数据，这就是我们所常说的超时重传。

TCP会在一下两种情况发生超时重传：

1.数据包丢失 2.确认应答丢失

RTT就是数据从网络一端传送到另外一端所需要的时间，就是包的往返时间。

超时重传时间是以RTO表示的，假设在重传的情况下，RTO较长或较短的情况：

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211216112338417.png" alt="image-20211216112338417" style="zoom:67%;" />

当超时时间RTO较大时，重发就慢，丢了老半天才发现，效率差

当超时RTO时间过小时，会导致可能没有丢就重发，会增加网络拥塞，导致更多的超时，更多的超时又会导致更多的重发。

所以精确的测量超时时间RTO的值是非常重要的，这可让我们的重传机制更高效

根据上述两种情况，我们可以得知，**超时重传时间RTO的值应该略大于报文往返RTT的值**

RTT的值实际上是经常变化的，因为我们的网络也是实时变化的，所以RTO的值也应该是一个动态变化的值。

估计往返时间RTT，通常需要采样以下两个：

1.TCP需要采样RTT的时间然后进行加权平均，算出一个平滑的RTT值，而且这个值还需要是不断变化的，因为网络的情况不断的变化。

2.除了采样RTT，还需要采样RTT的波动范围，这样可以避免如果RTT有一个大的波动的话，很难被发现的情况。、

**超时重发的数据，再次超时的时候又需要重传，TCP的策略是超时间隔加倍，也就是每当遇到一次超时重传的时候，下一次超时时间都会设置为先前值的两倍，两次超时就说明网络环境差，不宜频繁反复发送。**



超时触发重传问题在于超时周期可能相对较长，于是就可以用快速重传来解决超时重发的时间等待。

### 快速重传：

TCP的另外一种机制，快速重传，**它不以时间为驱动，而是以数据驱动重传。**

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211216114928690.png" alt="image-20211216114928690" style="zoom:50%;" />

如图发送方发送了五分数据：

Seq2由于某些原因丢失了，后面的数据也都到了，但是ACK还是回2，因为Seq没到，发送端收到三个ACK=2的确认就知道了Seq2没有收到，就会在定时器过期之前重传丢失的Seq2，

最后收到了Seq2，此时因为Seq3 4 5也收到了于是ACK返回6

快速重传就是当收到三个相同的ACK报文时，会在定时器过期之前重传丢失的报文段

快速重传机制只解决了一个问题，就是超时时间的问题，但是他**依然面临这另外一个问题就是，在重传的时候是重传之前的一个数据，还是重传所有的数据问题。**

为了解决不知道该重传哪些TCP报文，于是有了SACK方法。

### SACK：

SACK（选择性确认Selective Acknowledgement）

这种方法需要在TCP头部的选项字段中加一个SACK，他可以将缓存的地图发送给发送方，这样发送方就知道哪些数据被收到了，哪些没有，根据这些信息就知道该重传什么数据了。

### D-SACK：

这种主要是使用SACK来告诉发送方哪些数据被重复接收了。



## 滑动窗口：

我们都知道TCP每法送一个数据都要进行一次确认应答，当上一个数据包收到了应答，在发送下一个。

这个模式有点想我和你面对面聊天，你一句我一句，但是这种方式的缺点就是效率比较低。

如果你说完一句话我在处理其他事情没有及时回复你，那你就需要等我把事情做完后我回复了你你才能继续下一步，这显然很不显示。

所以这样的传输方式有个缺点：RTT越长通信效率越低

为了解决这个问题，TCP引入了**窗口**概念，即使在RTT较长的情况下，他也不会降低网络通信息效率。

那么有了窗口就可以指定窗口大小，**窗口大小**就是**无需等待ACK，而继续发送数据的最大值**

窗口的实现实际上是操作系统开辟的一个缓存空间，发送方主机在等到ACK返回之**前，必须在缓冲区中保留已发送的数据，如果按期接收到ACK，此时数据就可以从缓存区清除。**

假设窗口大小为3个TCP字段，那么发送发就可以连续发送3个TCP段，并且若中途有ACK丢失，可通过下一个ACK进行确认。

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211216120548015.png" alt="image-20211216120548015" style="zoom:50%;" />

如图中的600丢失了，没关系，因为可以通过下一个ACK来进行确认应答，只要发送方收到了ACK700就以为着之前所有数据都收到了，这个模式叫做**累计应答**。

**窗口大小由哪一方决定？：**TCP头里有一个字段叫window就是窗口大小，这个字段是接收端告诉发送端自己还有多少缓冲区可以接收数据，于是发送端就可以根据这个接收端的处理能力来发送数据，而不会导致接收端处理不过来。

所以**通常窗口大小是由接收方来决定的。**

发送方发送的数据大小不能超过接收方大小，否则接收方无法正常接收到数据。

![image-20211216142237363](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211216142237363.png)

接收窗口和发送窗口大小是约相等的，因为滑动窗口并不是一成不变的，当接收方应用程序读取数据的速度非常快的话，这样接收窗口可以很快的就空缺出来，那么新的接收窗口大小，是通过TCP报文中的Windows字段告诉发送方，那么这个传输过程是存在延时的，所以是约等于的关系。



## 流量控制：

发送方不能无脑的发送数据给接受方，要考虑接收方的处理能力，如果一直无脑发送数据给对方，但对方处理不过来，那么就会导致触发重发机制，从而导致网络流量的无端浪费。

为了解决这个现象的出现，**TCP提供了一种机制可以让发送方根据接收方的实际接收能力控制发送的数据量，这就是所谓的流量控制。**

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211216143218620.png" alt="image-20211216143218620" style="zoom:50%;" />

### 操作系统缓冲区和滑动窗口的关系：

上面流量控制的例子，我们假定了发送窗口和接收窗口是不变的，但实际上，发送窗口和接收窗口所存放的字节数，都是放在操作系统内存的缓冲区的，而操作系统的缓冲区，会被操作系统调整。

当应用进程没办法及时读取缓冲区内容时,也会对我们的缓冲区造成影响。

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211216150819774.png" alt="image-20211216150819774" style="zoom: 80%;" />

1.客户端发送了140字节的数据，于是可用窗口减少到了200

2.服务器因为现在非常的忙，操作系统于是把接收缓存减少了120字节，当收到140字节后，又因为应用程序没有读取任何数据，所以140字节留在了缓存区中，于是接收窗口大小从360收缩成100最后发送确认信息时，通告窗口大小给对方。

3.此时客户端因为还没有收到服务端的通告窗口报文，所以不知道此时接收端窗口收缩成了100，客户端只会看自己的可用窗口还有220，所以客户端就发送了180字节数据，于是可用窗口减少到40.

4.服务端收到180字节数据，但是发现数据大小超过了接收窗口的大小，于是就把数据包丢失了。

5.客户端收到第二步时，服务端发送的ACK和通告窗口报文，尝试减少报文发送到100，把窗口右端向左收缩了80，此时的可用窗口大小就出现了诡异的负值

所以，如果发生了先减少缓存再收缩窗口就会出现丢包情况。

为了防止这种情况发生，TCP规定是不能同时减少缓存又收缩窗口的，只能是先采用收缩窗口，过段时间在减少缓存，这样就避免了丢包的情况。

### 窗口关闭：

在前面我们看到了,TCP通过让接收方致命希望从发送方接收的数据大小（窗口大小）来进行一个流量的控制。

如果窗口大小为0时，就会组织发送方给接收方传递数据，直到窗口变为非0位置，这就是窗口关闭。

接收方发送通告窗口大小时，是通过ACK来通告的，那么当发生窗口关闭时，接收方处理完数据后，会向发送方通告一个窗口非0的ACK报文，如果这个通告窗口的ACK报文在网络中丢失了，那麻烦就很大了。

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211216152232432.png" alt="image-20211216152232432" style="zoom:67%;" />

这会导致发送方一直等待接收方的非0窗口通知，接收方也一直等待发送方的数据，如果不采取措施，这种相互等待就会出现死锁现象。

### TCP如何解决窗口关闭潜在的死锁现象呢？：

为了解决这个问题，TCP为每个链接设有一个持续定时器，只要TCP**连接一方接收到对方的零窗口通知**，就启动持续计时器。

如果持续计时器超时，就会发送窗口探测报文，而对方在确认这个探测报文时，给出自己现在的接收窗口大小。

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211216163811147.png" alt="image-20211216163811147" style="zoom:67%;" />

如果接收窗口仍然为0，那么这个报文的一方就会重新启动持续计时器

如果接收窗口不是0，那么死锁的局面就可以被打破了。

窗口探测的次数一般为3次，每次大约30~60S，如果三次过后接收窗口还是0的话，有的TCP实现就会发RST报文来中断连接。

### 糊涂窗口综合症：

如果接收方太忙了，来不及取走接收窗口里的数据，那么就会导致发送方的发送窗口越来越小。

到最后，**如果接收方腾出几个字节并告诉发送方现在有几个字节的窗口，而发送方会义无反顾发送这几个字节，这就糊涂窗口综合征**



## 拥塞控制：

虽然前面已经说过了流量控制，但**流量控制是避免发送方的数据填满接收方的缓存，但是并不知道网络中发生了什么，一般来说，计算机网络都处于一个共享的环境。因此也有可能会因为其他主机之间的通信使得网络拥堵。**

在网络出现拥堵时，如果继续发送大量数据包，可能会导致数据包时延、丢失等，这时TCP就会重传数据，但是一重传就会导致网络的负担更重，于是会导致更大的延迟以及更多的丢包，这个情况就会进入恶性循环。

因此 **拥塞控制**的目的就是**为了避免发送方的数据填满整个网络。**

为了在发送方调节所要发送的数据量就有了拥塞窗口概念，拥塞窗口cwnd是发送方维护的一个状态变量，他会根据网络的拥塞程度动态变化，我们在前面提到过发送窗口swnd和接收窗口rwnd是约等于的关系，那么假如拥塞窗口的概念之后，**发送窗口的值是swnd（send window）=min(cwnd（crowd window）,rwnd（recieve window）)**，也就是拥塞窗口和解说窗口中的最小值。

拥塞窗口cwnd变化规则：

只要网络中没有出现拥塞，cwnd就会增大

网络中出现了拥塞,cwnd就会减少

一般来说，只要发生了超时重传，就会认为网络中出现了拥塞。

**拥塞控制的主要四个算法：慢启动、拥塞避免、拥塞发生、快速恢复**

### 慢启动

TCP在刚建立连接完成后，首先是有个慢启动的过程，这个慢启动过程就是一点点的提高发送数据报数量

慢启动算法规则：当发送方每收到一个ACK时，拥塞窗口cwnd大小就会+1

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211220145128549.png" alt="image-20211220145128549" style="zoom:67%;" />

拥塞窗口会呈指数型增长

当cwnd超过慢启动门限ssthresh时候就会进入新的算法拥塞避免算法

拥塞避免算法规则：每当收到一个ACK，cwnd增加1/cwnd

这样就会从原来的指数型增长变为线性增长，虽然还是增长，但是速度缓慢了一些。直到触发重传机制，就会进入拥塞发生算法。

### 拥塞发生：

当网络中出现拥塞时，也就是会发生数据包重传，超时重传和快速重传。

拥塞发生算法对于这两种重传方式是不同的。

**超时重传的拥塞发生算法**此时ssthresh 设置为cwnd/2 而cwnd重置为1

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211220152119980.png" alt="image-20211220152119980" style="zoom:67%;" />

**快速重传的拥塞发生算法**：

我们直到快速重传是接收方发现中间丢了一个包后，发送三次前一个包的ACK，于是发送端就会进行快速地重传而不需要等待超时在重传。

TCP认为这种情况不严重，因为大部分未丢失，只丢失了小部分，所以ssthresh和cwnd变化如下：

​	cwnd = cwnd/2 ，也就是设置为原来的一半。

​	ssthresh = cwnd 

​	进入快速恢复算法

### 快速恢复

快速重传和快速恢复算法一般同时使用，快速恢复算法是认为，你还能受到三个重复的ACK就说明网络状况没有那么糟糕所以没有必要像超时重传那样那么强烈。

进入快速恢复算法：

拥塞窗口cwnd = ssthresh + 3 (3的意思是确认有三个数据包收到了)

重传丢失的数据包

如果在收到重复ACK 则 cwnd+1

如果收到新数据的ACK后，把CWND设置为第一步中ssthresh 的值，原因是该ACK确认了新的数据，说明从D-SACK时重发的数据都已经收到，该恢复过程结束，可以恢复到之前的状态了，也就是再次进入拥塞避免状态





