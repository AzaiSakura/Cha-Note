## JS基础知识点（3）：JS内存结构、深浅拷贝、JSON拷贝、JS运行机制、eventloop、宏任务与微任务、promise与async、



## js的内存结构：

### js的原始内存（栈内存）和引用内存（堆内存）

![image-20211201203424961](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211201203424961.png)

![image-20211201203517178](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211201203517178.png)

### 引出疑问：为什么同样是复制 第一次就是20 10 第二次就全是一样的呢？

### 简单（原始）数据类型：  number 、string、null、undefined、boolean、symbol  

### 复杂（引用）类型：Object

对象是存储在堆内存之中的

![image-20211201203758093](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211201203758093.png)

### 因为原始类型的变化都在栈内存里面，所以最后能够看到20 10 我们想要的这个结果。

### 而因为第二次 a 是一个对象，对象存储在堆内存之中，a指向chacha，把b=a 就可以理解为，让b也指向这个堆内存地址,所以之后，a修改了name之后，这个堆内存里面的内容就变了，自然而然b的值也跟着会变了



## 对象拷贝之---浅拷贝

### 问题抛出：

![image-20211201213301585](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211201213301585.png)

在这种情况下我们修改一个新添加的其他已经添加好的就都会变

![image-20211201213331867](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211201213331867.png)

#### 这并不是我们想要的效果，原因呢是因为List 中 我们push的是student的对象，也就是说这个list里面现在全部都是student这个对象，因为对象是引用类型，内存放在堆中，所以修改一个新的加入就会导致那个堆地址的变量改变，也就是让其他的都改变了的原因。

那么要如何解决问题呢，那就是要让每次都要创建一个新的对象来加入这个list，这个就是对象拷贝的应用了。

### 浅拷贝代码：

```js
//解决上述问题，我们在栈内存中每次都创建一个新对象 
		let obj = {
             name:"chacha",
             age:2
         }
        function copy(obj){
            let newObj = {};
            for (let i in obj){
                newObj[i] = obj[i];
            };
            return newObj
        }
        let newObject = copy(obj);
//这样就把堆内存的数据位置给了新的newObject  就是创建了一个newObject并且也让他指向了name和age 这就是浅拷贝
```

## 

## 深度拷贝：

可以看到 上面的那个拷贝，如果obj的属性中还有一个对象属性

```js
let obj = {
	name:"chacha",
	age:2,
	girlfriend:{
		name:"yinghua"
	}
}
//可以根据上面浅拷贝的代码发现，newObj[i]= obj[i],意思就是复制的时候就算是对象也一同复制，就会导致出所有girlfriend对象是同一个，会出现上面一改全改的问题。
```

这个时候为了让我们同事也能复制到包括对象里面的属性，我们就需要用到深拷贝。

### 通过递归来实现深拷贝：

```js

function Deepcopy(obj){
            let newObj = {};
            for (let item in obj){
                if(item instanceof Object){
                    newObj[i] = Deepcopy(obj[i])
                }else{
                newObj[i] = obj[i];
                }
            };
            return newObj
        }
//就是在复制的时候判断当前属性是否为一个对象，如果是，那么就递归在给他复制一次
```



## Json拷贝：

json（javascript对象表示法）

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211201220026499.png" alt="image-20211201220026499" style="zoom:67%;" />

### 说白了就是一个对象数组但是需要注意json的属性中的属性名也需要加上双引号

json是一种数据格式，是目前项目开发的前后端交互的数据格式

### JSON.stringify能够将一个对象变成一个字符串

![image-20211201220501297](C:\Users\11791\Desktop\前端\前端面试题\JS类\image-20211201220501297.png)

但是内容还是一样的

### JSON.parse()就可以将json变成一个对象

![image-20211201220641786](C:\Users\11791\Desktop\前端\前端面试题\JS类\image-20211201220641786.png)

这样就能实现JSON的深度克隆。

### 需要注意：JSON拷贝中 如果数据是正常的倒是可以正常完成深度克隆的任务，但是当会遇到特殊值时候JSON拷贝就会出现问题，比如NAN和undefined还有null的时候json拷贝就会直接略过，所以在使用JSON拷贝之前需要考虑清楚是否会出现undefined或者NAN这样的特殊数据

### 新增：在2022年一月份中,nodejs 17.4版本中新推出了JSON.sructuredClone() 这个API来直接调用JSON拷贝



## Javascript运行机制

JS有一个基于事件循环的并发模型，事件循环负责执行代码、收集和处理事件以及执行队列中的子任务。这个模型与C和Java的模型截然不同。

## 永不阻塞

**JavaScript的事件循环模型与许多其他语言不同的一个非常有趣的特性是，它永不阻塞。 处理 I/O 通常通过事件和回调来执行**，所以当一个应用正等待一个 [IndexedDB](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API) 查询返回或者一个 [XHR](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 请求返回时，它仍然可以处理其它事情，比如用户输入。

## 运行时概念

这是JS的 运行时 模型的描述

![Stack, heap, queue](https://mdn.mozillademos.org/files/17124/The_Javascript_Runtime_Environment_Example.svg)

### 栈内存

函数调用形成了一个若干帧（Frame）组成的栈

```js
function foo(b) {
  let a = 10;
  return a + b + 11;
}

function bar(x) {
  let y = 3;
  return foo(x * y);
}

console.log(bar(7)); // 返回 42
```

当我们调用bar时，第一个帧被创建并且压入栈中，帧包含了bar的参数和局部变量。当bar调用foo时候，第二个帧被创建且压入栈中，放在第一个帧之上，帧中包含foo的参数和局部变量。当foo执行完毕并且返回时候，第二个帧就被弹出栈。当bar也执行完毕返回时候第一个帧也出栈，栈就被清空了。

### 堆内存

对象被分配在堆中，堆是一个用来表示一大块（通常是非结构化的）内存区域的设计

### 队列

一个JS运行时包含了一个待处理的消息队列。每一个消息都关联着一个用以处理这个消息的回调函数。**在事件循环期间的某个时刻**，运行时候会从最先进入队列的消息开始处理消息，被处理的消息就会出队，并作为输入参数来调用与之关联的函数。正如前面所提到的，**调用一个函数总是会为其创造一个新的栈帧。**

**函数的处理会一直进行到执行栈空位为止；然后事件循环将会处理队列中的下一个消息。**

## 事件循环

之所以称为事件循环，是因为经常按照类似如下的方式来被实现：

```js
while (queue.waitForMessage){//等待消息到来
    queue.processNextMessage();
}
```

### 事件循环：不断检测任务队列有无任务

### “执行至完成”

**每一个消息完整地执行后，其他消息才会被执行。**这为程序的分析提供了一些优秀的特性；包括：**当一个函数执行时，他不会抢占，只有在它运行完毕之后才会去运行任何其他代码，才能修改这个函数操作的数据**。这个与C语言不太同，例如，如果函数在线程中运行，它可能在任何位置被终止，然后在另一个线程中运行其他代码。

这个模型的缺点在于当一个消息需要太长时间才能处理完毕时候，Web应用程序就无法处理与用户的交互，例如点击或滚动。为了缓解这个问题，浏览器一般会弹出一个“这个脚本运行时间过长”的对话框。一个良好的习惯是缩短单个消息处理时间，并在可能的情况下将一个消息剪成多个消息。

## 多个运行时互相通信：

一个web worker或者一个跨域的iframe都有自己的栈、堆和消息队列。两个不同的运行时只能通过postMessage方法进行通信，如果另一个运行时侦听message事件，则此方法会向该运行时添加消息。

### 同步与异步：

**异步宏任务：** 1. 计时器 2. ajax请求 3.读取文件

#### 主线程中，同步程序执行之后才会执行异步程序

## 同步任务就是同步任务，为什么异步任务就需要分宏任务和微任务呢？

**微任务是线程之间的切换，速度快。**不用进行上下文切换，可以快速的一次性做完所有的微任务。

**宏任务是进程之间的切换，速度慢，**且每次执行需要切换上下文。因此一个Eventloop只执行一个宏任务。

**而区分微任务和宏任务的根本原因是为了插队。**由于微任务执行快，一次性可以执行很多个，在当前宏任务执行后立刻清空微任务队列可以达到一种伪同步的效果，这对于视图的渲染起到了至关重要的重要。



### 单线程：

js是单线程的，一个任务完成之后才能执行另一个任务

**那么其实计时器是不准的，因为如果前面的代码处理要很长时间，那么计时器也需要先等同步代码执行完毕。**其等待时间取决于**队列里待处理的消息数量**，在下面的例子中，“这是一条消息”将会在回到获得处理之前输出到控制台，这是因为延迟参数是运行时处理请求所需最小等待时间，但并不保证是准确的等待时间。**计时器是等计时到了之后才把任务插入到任务队列里面的**

**基本上，setTimeout需要等待当前队列中所有消息都处理完毕之后才执行，即使已经超出了第二参数所指定的时间。**

```js
(function() {

  console.log('这是开始');

  setTimeout(function cb() {
    console.log('这是来自第一个回调的消息');
  });

  console.log('这是一条消息');

  setTimeout(function cb1() {
    console.log('这是来自第二个回调的消息');
  }, 0);

  console.log('这是结束');

})();

// "这是开始"
// "这是一条消息"
// "这是结束"
// "这是来自第一个回调的消息"
// "这是来自第二个回调的消息"

```



### node环境中的process.nextTick(()=>{ })   他是在同步代码之后，异步代码之前执行nextTick()



顺序：1.同步 2.process.nextTick() 3.异步微任务 4. 异步宏任务

setImmediate（（）=>{ ..}）这个计时器在 当前本次事件循环最后

```javascript
setImmediate(()=>{
    console.log(1)
})
process.nextTick(()=>{
    console.log(2)
})
console.log(3);
setTimeout(()=>{ console.log(4)},0)
setTimeout(()=>{ console.log(5)},1000)
setTimeout(()=>{ console.log(6)},0)
console.log(7);
//输出顺序3724615   5号因为有1000的等待时间 所以是在第二次事件循环，所以在setImmediate后面
```

## 宏任务和微任务：

异步代码又分为 宏任务和微任务

**宏任务：计时器、ajax、读取文件**

**微任务： promise.then**

## 严格记住：js执行顺序

一次事件循环包括以下：

1.同步代码  2. process.nextTick() 3. 微任务和宏任务 4.setImmediate（在当次事件循环的最后）

执行完后进入下一次事件，直到任务队列为空

做Eventloop题的时候，await后面的函数是同步代码

promise的函数参数也是同步代码

## 

## EventLoop例题

```js
new Promise((resolve, reject) => {
  console.log(1)
  resolve(2)
}).then((data) => {
  // 1号回调
  console.log(data);
  return 3
}).then((data) => {
  // 2号回调
  console.log(data);
})

new Promise((resolve, reject) => {
  console.log(5)
  resolve(6)
}).then((data) => {
  // 3号回调
  console.log(data);
  return 7;
}).then((data) => {
  // 4号回调
  console.log(data);
})
//152637
```

1.new Promise的函数参数是同步代码，所以先打印1。同时将第一个then的回调（1号回调）放入微队列。

2.同理，打印5，将第二个new Promise的第一个then的回调（3号回调）放入微队列。

3.执行1号回调，打印2，将2号回调放入微队列。

4.执行3号回调，打印6，将4号回调放入微队列。

5.执行2号回调，打印3。

6.执行4号回调，打印7。

```js
setTimeout(() => {
  console.log(1)
  setTimeout(() => {
    console.log(2)
  })
})

setTimeout(() => {
  console.log(3)
  setTimeout(() => {
    console.log(4)
  })
})
//1324
```

1.观察两个setTimeout的时间都是默认0

2.执行setTimeout, 输出同步代码1，将里面里面的定时器放入宏任务队列

此时宏任务队列为2

3.执行第3个SetTimeout，先输出同步代码3，再将自己里面的定时器加入宏任务队列

此时宏任务队列为24

4.执行第一个定时器的回调 输出2

5.执行第三个定时器的回调输出4

```js
console.log('script start');

async function async1() {
    await async2();
    console.log('async1 end');
};

async function async2() {
    console.log('async2 end');
};

async1()

setTimeout(() => {
    console.log('setTimeout')
}, 0)

new Promise((resolve, reject) => {
    console.log('promise start');
    resolve()
})
.then(() => console.log('promise end'))

console.log('script end')
//scrpit start async2 end  promise start script end  async1 end promise end setTimeout
```

1.打印script start

2.执行async1(),async1的第一行是await async2(),这一句应该拆开考察其中async2()会同步执行，await下面的代码加入微任务队列（async1 end）

3.执行setTimeout,不打印，同时把回调放入宏任务队列

4.执行new promise函数参数,打印promise start 同时把then的回调放入微任务对了，此时，微任务队列就有了两个代码（async1 end  promise end）

5.打印console.log(script end);

6.此时，本次循环中的同步部分已经全部打印，开始打印异步部分，先清空微任务队列，所以

先输出async1 end

7.继续清微任务队列, promise end 

8.执行宏任务  打印 setTimeout

### 证明await的赋值操作是异步任务

```js
let x = 5;
let y;
function ret() {
    x += 1;
    console.log('x是', x);
    console.log('y是', y);
    return x;
}
async function a() {
    y = await ret();
    console.log(y);
}
async function b() {
    y = await ret();
    console.log(y);
}
a()
b()   
//输出：x是 6 y是 undefined  x是 7 y是 undefined 6 7
//执行await赋值将await下面代码加入微任务队列， b中的await也是一样的
```

我们用反证法，假定await赋值是同步操作，那么`a()`的`y = await ret()`会同步执行，之后才执行`b()`的`ret()`，此时y有值，不应该是`undefined`，但事实是`undefined`，说明await赋值是异步操作。

### **证明newPromise()函数参数和await后面的函数是同步任务**，证明很简单：

```js
var i ;
for(i=0;i<20;i++){
    new Promise(resolve => {
        console.log(i);
    })
}
```

这里我们用var声明i让其变量声明提升，并且没有块级作用域

如果说new Promise是异步任务，那么就会和setTimeout一样打印20个20，但实际上会打印1到20，说明new Promise函数参数中是同步任务

同样的方法我们也可以用来测试await

```js
function log(x) { console.log(x) }
var i;
for (i = 0; i < 20; i++) {
    async function a() {
        await log(i)
    }
    a();
}
```

打印1-20,说明await后面的函数是同步的

### 证明await会暂停循环

```js
var i;
function ret() {
    console.log('ret里的i', i);
    return 100;
}
async function a() {
    for (i = 0; i < 20; i++) {
        await ret();  //我们知道await下面的代码会被加入微任务队列
        console.log(i);
    }
}
a();//输出1-20，说明虽然微任务队列放了20个微任务，但是由于await能让循环暂停，所以并不会像定时器那样输出20个20
```

## EventLoop总结：

1.随时遇到异步，随时放入队列，这是最起码的准则。

2.先同步任务，同步任务有异步回调的时候，根据规则放入队列

3.同步任务完成了就继续执行微任务队列，如果有异步则继续放入队列

4.微任务完毕就开始执行宏任务队列第一个，如果有异步则继续放入队列

5.观察微任务队列，有则执行，没有就执行宏任务队列第二个

6.重复3  4 5步骤直至任务队列都为空

## Promise和async

```js
let p = new Promise((resolve)=>{
	resolve("hello world");
})
p.then((data)=>{
	console.log(data)
}) 
//then方法只有会在resolve被调用的时候才会被调用
//then里面的data就是resolve返回出来的值
```

### async是promise的一个语法糖

async函数的返回值是一个promise对象

```js
async function fun(){
    return 1;
    //此时这个1也是promise对象
}
//要取到这个1
fun().then((data)=>{ console.log(data)})//输出的就是1
```

await +promise对象就能拿到then的值

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211202120619191.png" alt="image-20211202120619191" style="zoom:50%;" />

这个就是await的用法，await后面的代码可以当做是then中的代码。



