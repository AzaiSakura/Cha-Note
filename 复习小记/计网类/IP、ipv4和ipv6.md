# IP、ipv4和ipv6

当我们访问网址的时候，网址会被转化成一串数字，即IP地址

![image-20211106220424999](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211106220424999.png)

因为IP地址内容有限，为了防止IP地址枯竭问题，所以我们提出IPv6的协议。

IPv6是一个128位的地址，另外IPv6有更好的扩展性以及安全性，但是IPv4和IPv6互不兼容，因此不仅仅需要我们所使用的设备 包括手机电脑等等支持IPv6协议，还要网络运营商也要对现有的设备进行升级，这也是为什么这么多年过去了Ipv6使用率低的原因

### 二进制

![image-20211106230045795](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211106230045795.png)

255=128+64+32+16+8+4+2+1

所以是对应二进制位上全是1

## ipv4中的IP地址每一组之间都是有8位二进制表示的

![image-20211106230234243](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211106230234243.png)

这样ipv4地址加起来就有32位

![image-20211106230314120](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211106230314120.png)

## IP地址：网络号 + 主机号

![image-20211106230545963](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211106230545963.png)

为了解决IP分配不合理导致的问题，就出现了子网掩码。

子网掩码就是为了划分网络号和主机号的。

### 子网掩码：

用二进制的1进行锁定位，锁定的位置的就是网络号，不锁定的就是端口号

![image-20211107113536739](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211107113536739.png)

这样就是子网掩码了