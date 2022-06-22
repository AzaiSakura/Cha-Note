## Cookie、session、token、localstorage、seessionstorage

### 1.cookie：

cookie是纯文本，没有可执行代码，是指某些网站**为了辨别用户身份、进行 session 跟踪而储存在用户本地终端（浏览器）上的数据**（通常经过加密）。当用户访问了某个网站的时候，我们就可以通过cookie在访问者电脑上存储数据，或者某些网站为了辨别用户身份、进行session 跟踪而将数据储存在用户本地终端上（通常经过加密）。

**cookie默认是临时存储的，浏览器关闭进程的时候就会自动销毁**，如果要长时间保存一个cookie那么就需要给cookie设置一个过期时间，当Web服务器创建了cookies后只要在有效期内，用户访问同一个Web服务器时，浏览器会首先检查本地的Cookies并将其原样发送给Web服务器，cookie是能保持Web浏览状态的重要手段。

**有两种方法可以确保 `Cookie` 被安全发送，并且不会被意外的参与者或脚本访问：`Secure` 属性和`HttpOnly` 属性。**

标记为 **`Secure`** 的 Cookie **只应通过被 HTTPS 协议加密过的请求发送给服务端**，因此可以**预防中间人攻击者的攻击**。但即便设置了 `Secure` 标记，**敏感信息也不应该通过 Cookie 传输，因为 Cookie 有其固有的不安全性**，`Secure` 标记也无法提供确实的安全保障, 例如，可以访问客户端硬盘的人可以读取它。

工作过程：

（1）首先，我们假设当前域名下还是没有 Cookie 的

（2）接下来，浏览器发送了一个请求给服务器(这个请求是还没带上 Cookie 的)

（3）服务器设置 Cookie 并发送给浏览器(当然也可以不设置)

（4）浏览器将 Cookie 保存下来

（5）接下来，以后的每一次请求，都会带上这些 Cookie，发送给服务器



### Cookie、session、localStorage区别：

cookie主要包括：name , value, timeout , path ,origin 路径和域一起构成cookie的作用范围。

随浏览器会关闭而消失的cookie被称为会话cookie

当程序需要为某个客户端创建一个session时，服务器首先会检查这个客户端的请求是否已包含一个session_id ,如果已经包含则说明已经为此客户端创建过session，服务器就按照这个session_id把session检索出来使用，如果不包含则会创建一个session（seesion的形式就很像购物商场里面存物柜子，吐出来的小票就像cookie）

## Websotrage

Web Storage的概念和cookie相似，区别是它是为了更大容量存储设计的，**cookie的大小是受限的，并且每次请求一个新的页面的时候cookie都会被发送过去，这样无形中浪费了带宽**，另外cookie还需要指定作用域，不可跨域调用。 
除此之外，web storage拥有**setItem,getItem,removeItem,clear**等方法，不像cookie需要前端开发者自己封装setCookie，getCookie。 
但是cookie也是不可或缺的，**cookie的作用是与服务器进行交互，作为http规范的一部分而存在的**，而web Storage仅仅是为了在本地“存储”数据而生 

sessionStorage、localStorage、cookie都是在浏览器端存储的数据，其中sessionStorage的概念很特别，引入了一个**“浏览器窗口”**的概念，sessionStorage是在一个同源的同窗口中，始终存在的数据，也就是说只要这个浏览器窗口没有关闭，**即使刷新页面或进入同源另一个页面，数据仍然存在，关闭窗口后，sessionStorage就会被销毁，同时“独立”打开的不同窗口，即使是同一页面，sessionStorage对象也是不同的**

### Web Storage带来的好处： 

1、减少网络流量：一旦数据保存在本地之后，就可以避免再向服务器请求数据，因此减少不必要的数据请求，**减少数**

**据在浏览器和服务器间不必要的来回传递** 
2、**快速显示数据：性能好，从本地读数据比通过网络从服务器上获得数据快得多，本地数据可以及时获得，再加上网**

**页本身也可以有缓存，因此整个页面和数据都在本地的话，可以立即显示** 
3、**临时存储：很多时候数据只需要在用户浏览一组页面期间使用，关闭窗口后数据就可以丢弃了，这种情况使用sessionStorage非常方便**



**浏览器本地存储与服务器端存储的区别** 
其实数据既可以在浏览器本地存储，也可以在服务器端存储 
浏览器可以保存一些数据，需要的时候直接从本地读取，sessionStorage、localStorage和cookie都是由浏览器存储在本地的数据 
**服务器端也可以保存所有用户的所有数据，但需要的时候浏览器要向服务器请求数据。** 
1、服务器端可以保存用户的持久数据，如数据库和云存储将用户的大量数据保存在服务器端 
2、服务器端也可以保存用户的临时会话数据，服务器端的session机制，如jsp的session对象，数据保存在服务器上

**sessionStorage、localStorage和cookie的区别** 

共同点：**都是保存在浏览器端、且同源的** 
区别： 
1、cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递，而sessionStorage和localStorage不会自动把数据发送给服务器，仅在本地保存。**cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下** 
2、**存储大小限制也不同，cookie数据不能超过4K，同时因为每次http请求都会携带cookie**、所以cookie只适合保存很小的数据，如会话标识。sessionStorage和localStorage虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大 
3、数据有效期不同，sessionStorage：仅在当前浏览器窗口关闭之前有效；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie：只在设置的cookie过期时间之前有效，即使窗口关闭或浏览器关闭 
4、作用域不同，**sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；localstorage在所有同源窗口中都是共享的**；**cookie也是在所有同源窗口中都是共享的** 
5、web Storage支持事件通知机制，可以将数据更新的通知发送给监听者 
**6、web Storage的api接口使用更方便**

### Cookie、session、token关系：

1、cookie

用户登录成功后**，会在服务器存一个session，同时发送给客户端一个cookie**，数据需要客户端和服务器同时进行存储**，用户进行操作时，需要带上cookie，在服务器进行验证，另外cookie是有状态的**

2、token

用户将进行任何操作都需要带上一个token，**token存在形式是多样的，header   request.body   url都可以，这个token只需要存在客户端，服务器在收到数据后，进行解析，token是无状态的。**

对比：

**token相比于cookie更加先进更加安全，更适用于跨平台、移动平台等、**
