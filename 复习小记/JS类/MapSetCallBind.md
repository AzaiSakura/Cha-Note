## Map、Set、WeakMap、WeakSet、Call、Bind、apply

#### const和let声明的量并不指向window对象，只有var声明的才指向window

#### WeakSet和Weakmap  不能迭代 没有foreach() 没有size属性 不能传入非对象类型的参数

### 3.弱引用对象： 将弱引用对象设置为Null 之后会被垃圾回收机制回收

![a9b17a7f5de53db81ac1e1b1c8f01219.png](https://img-blog.csdnimg.cn/img_convert/a9b17a7f5de53db81ac1e1b1c8f01219.png)

## 先讲一讲强引用、弱引用、软引用、幻象引用的区别：

这方面知识看起来是比较冷门，但是实际上在我们编程过程中是处处常见的。比如，我们在用new创建一个对象的时候就是强引用的应用。

在java语言中，除了原始数据类型（boolean、byte、short....）的变量，其他所有都是所谓的**引用类型**，指向各种不同的对象。理解这些引用的区别，对于掌握java对象生命周期和JVM内部相关机制非常有帮助。

### 1.强引用（strong reference）

强引用就是我们最常见的普通对象的引用比如new一个对象，只要强引用指向一个对象那么就表明这个对象还“活着”，在强引用面前，即JVM内存空间不足，JVM宁可抛出错误让程序异常终止，也不会靠回收强引用来解决内存不足的问题。对于一个普通的对象，如果没有其他的引用关系，只要超过了引用的作用域或者显示地将相应（强）引用赋值为null，就意味着对象可以被垃圾回收，但是**需要注意的时是，并不是赋值为null之后就立马会被垃圾回收，具体回收的时机还是要看垃圾回收策略的**。比如，Object obj =new Object();

### 2.软引用(soft reference)

软引用相对于强引用要弱化一些，“可以让对象豁免一些垃圾收集，垃圾收集器不会回收它”，只有当JVM认定内存不足的时候才会去回收软引用指向的对象，JVM会保证在抛出OOM前清理软引用指向的对象，而且JVM是很聪明的，会尽可能优先回收长时间限制不用的软引用指向的对象，对那些刚构建的或使用过的软件引用指向的对象尽可能保留。基于软引用的这些特性，可以用来实现很多内存敏感点的缓存场景，即如果内存还有空间，可以暂时缓存一些业务场景所需数据，当内存不足时，可以清理掉，等后面需要时候再次进行缓存，这样就确保使用缓存提升性能同时，不会导致耗尽内存。

软引用通常可以和一个引用队列（RefenceQueue）联合使用，如果引用所引用的对象被垃圾回收，java虚拟机会把这个软件引用加入到与之关联的引用队列中。

```java
Object obj = new Object();
SoftReference<Object> sf = new SoftReference<Object>(obj);
obj = null ;
//有时候会返回null
sf.get();
//当sf对象没被销毁前可以获取到这个对象，但是如果已经被销毁则返回null
//正确使用姿势
if(sf != null){
    sf.get().add(foo);
}else{
    //sf has been destroyed
}
```

所以，在使用软引用的时候必须检查是否为null,因为垃圾收集器可能在任意时刻回收软引用，如果不做是否为null的判断就可能抛出空指针异常。

### 3、弱引用（weak reference）

弱引用指向的对象是一种十分邻近finalize（最终完成）状态的情况，当弱引用被清除的时候就符合finalize的条件了。弱引用和软引用最大的区别就是弱引用的生命周期比起软引用更加短暂，**垃圾回收器会扫描它所管辖的内存区域的过程中，只要发现弱引用对象，不管内存空间是否有空闲，都会立刻回收它，如同前面提到过的，具体回收的时机还是要看垃圾回收策略，因此那些弱引用的对象并不是说只要达到弱引用状态就会立即被回收。**

基于弱引用的特性，弱引用同样可以应用在很多需要缓存的场景。

```java
Object obj = new Object();
WeakReference<Object> wf = new WeakReference<Object>(obj);
obj = null ;
//有时候会返回null
wf.get();
//返回是否被垃圾回收器标记为即将回收的垃圾
wf.isEnQueued();
```

### 4.幻象引用(phantom reference)

幻象引用，也有被说是虚引用或者幽灵引用的。幻象引用并不会决定对象的生命周期。即如果一个对象仅持有虚引用，就相当于没有任何引用一样在任何时候都可能被垃圾回收器回收。不能通过它访问对象，幻象引用仅仅提供了一种确保对象被finnalize以后，做某些事情的机制，也有利用幻象引用控制对象的创建和销毁。

```java
Object obj = new Obeject();
PhantomReference<Object>pf = new PhantomReference<Object>(obj);
obj =null ;
//永远返回Null
pf.get();
//返回是否从内存中已经删除
pf.isEnQueued();
```





## Map（字典）

一个Map对象在迭代时会根据元素插入的顺序来进行一个遍历，for..of循环在每次迭代后都会返回一个[key,value]的数组。

### 键的相等

键的比较式基于sameValueZero算法；

NaN是与NaN相等的（虽然NaN !== NaN），剩下所有其他的值根据 === 运算符的结果判断是否相等。

**Map的键名可以是任意值，但是对象的键名只能是字符串**

与Set的相同点：Map、Set 可以存储不重复的值

不同点：Set是以[value,value]的形式来存储元素的，而Map是以[key,value]的形式进行存储的

任何一个具有iterator接口且每个成员都是一个双元素的数组的数据结构都可以当做Map构造函数点的参数

```js
//例如
const m = new Map();
const o = {p: 'haha'}
m.set(o,'content')
m.get(o)  //content
m.has(o) //true
m.delete(o) //true
m.has(o) //false
```

map的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等（===），Map会将其视为一个键

这就解决了同名属性碰撞的问题，比如0和-0就是一个键，因为用===比较是true，另外 undefined和null 以及 NAN和NAN 会被视为一个键。

## Map与Object的区别

### 键的类型：

Map的键可以是人一直，而Object的键必须是symbol或者String

### 键的顺序：

Map的key是有序的，因此每当迭代的时候,一个Map对象以插入的顺序返回键值。而Object的键是无序的。

### Size：

Map的键值对个数可以通过size属性来获取，而Object的键值对个数只能手动计算

### 迭代：

Map是iterable的，所以可以直接被迭代。迭代一个Object需要以某种方式获取它的键之后才能迭代。

### 性能：

在频繁增删键值对的情况下Map表现更为良好，而Object并未作出优化。

## WeakMap

WeakMap对象是一组键值对的集合，其中**键是弱引用对象，而值是可以任意。**

### 注意，WeakMap弱引用的只是键名，而不是键值，键值依然是正常引用的。

#### 且WeakMap的键必须是对象，而值是可以任意的。

WeakMap中，每个键对自己所引用对象的引用都是弱引用，在没有其他引用和改键引用同意对象，这个对象会被垃圾回收，所以**WeakMap的key是不能枚举的**

### Why WeakMap：

在JS中，map API 可以通过其4个API方法共用两个数组（一个存放键，一个存放值来实现），给这种map设值时会同时将键和值添加到这两个数组的末尾，从而使得键和值的索引在两个数组中对应。当从该map取值的时，需要遍历所有键，然后使用索引从存储值的数组中检索出相应的值。

但是这样的实现会有很大的两个缺点：

1.**首先赋值和搜索操作都是O(n)的时间复杂度（n是键值对的个数）**，因为这两个操作都需要遍历全部整个数组来进行匹配。

2.**另外一个缺点是可能会导致内存泄露，因为数组会一直引用着每个键和值。**这种引用使得垃圾回收算法不能回收处理他们，即使他们没有任意引用存在了。

相比之下，WeakMap持有的是每个键对象的“弱引用”，这意味着在没有其他引用存在时回收能够正确进行。**原生WeakMap的结构特殊且有效，其用于映射的Key只有在其没有回收时才有效。**

正式由于这样的弱引用，WeakMap的key是不可枚举的(没有方法能给出所有的key)，如果key是可以枚举的话，其列表会受垃圾回收机制的影响，从而得到不确定的结果。因此，如果你想要这种类型对象的key值，我们应该用Map。

## Set（集合）是一种构造函数，用来生成Set数据

Set对象是值的集合，你可以按照插入顺序来迭代它的元素。Set中的元素只会出现一次，即Set中元素是唯一的。

### 值的相等：

因为Set中的值宗室唯一的，所以需要判断两个值是否相等。在ECMA遭起规范中，这不是基于和===操作符中使用的算法相同的算法。具体来说就是，对于Set而言 ， +0 ！== -0  （而 +0===-0 返回true）

另外，NaN和undefined都可以被存储在Set中，NaN之间被视为相同的键（尽管NaN被认为是相同的，但是NaN！== NaN）

## WeakSet

WeakSet对象允许你将**弱引用对象**存储在一个集合中（即元素item都是对象）

### WeakSet与Set的区别：

​	**1.WeakSet只能储存对象引用，不能存放值，而Set对象都可以**

2.WeakSet对象中存储的对象之都是被弱引用的，即**垃圾回收机制不考虑WeakSet对该对象的应用**，如果没有其他的变量或者属性引用这个对象值，则这个对象会被垃圾回收掉，所以，WeakSet对象里有多少个成员元素，取决于垃圾回收机制有没有运行，运行前后的成员数可能不一样，遍历结束后，有的成员可能就取不到了，WeakSet对象是无法遍历的（ES6中规定WeakSet不可遍历），也没有办法能拿到它的所有元素。







## Prototype中constructor的作用

### prototype和__proto\_\_ 的区别  

### 后者是真正的原型链，使用原型链时，																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																							，而不是prototype，  prototype 只是用于构建用new关键字创建的对象，将高早期函数的prototype的引用指向于构建的对象的\_\_proto\_\_ 

###  

### constructor是什么

constructor是构造器函数的引用，一个构造器函数的prototype的constructor也就是构造器函数本身，一个构造器函数的\_\_proto\_\_ 的constructor，比如Function 的constuctor 就是Function 本身。



### 只有函数对象才有prototype属性，Object.\_\_proto\_\_  === Funciton._proto__

### Object._proto__.constructor === Object

###  ![img](https://images2015.cnblogs.com/blog/718921/201606/718921-20160601123901211-736874185.png)

## 



```js
a= (1,2) console.log(a)// 2 逗号运算符只返回表达式最后一个
```

```js
var a = 10; 
if(function b(){})
{ a += typeof(b); }
console.log(a); //10undefined   在if()小括号内 b被当做一个表达式不可以在外界被命名式调用，所以 typeof(b)中的b
//会被以为是未赋值的b 被直接挂载到window对象上面 所以是undefined
//另外 typeof 判断是永远不会报错的 最多只会爆undefined
```

```js
var A {n:4399};
var B = function (){this.n = 9999};
var C = function (){ var n = 8888};
B.prototype = A ;
C.prototype = A ;
var b = new B();
var c = new C();
A.n++;
console.log(b.n);//9999
console.log(c.n);//4400
```



## Call、Apply、bind方法的区别：

Call、Apply方法都是直接问执行函数，但是传入的第二个参数不相同，Call函数传入参数列表、Apply传入一个数组或者一个类数组

Bind则返回一个新的函数，调用Bind方法和返回函数的参数会进行合并，并且Bind作为构造函数调用，this会指向创建的实例对象

## Call：

call、bind、apply他们仨都是改变this指向的方法

```js
Function.prototype.myCall = function (context){
    if(typeof context === 'undefined'||context == null){
        context = window 
    }
    //context  = context || windowe
    const fn = new Symbol();
    cotext[fn] = this;
    const args = [...arguments].slice(1);
    const result = context[fn](...args);
    delete context[fn]
    return result;
}
```



#### fn.call

当前实例（函数fn）通过原型链的查找机制，找到function.prototype上面的call方法

function call(){[native code]}

#### fn.call() 把找到的call方法执行，当call方法执行的时候，内部处理了一些事情

1.首先把操作的函数的this关键词改变为call方法第一个传递的实参

2.把call方法第二个及以后的实参获取到

3.把要操作的函数执行，并且把第二个以后传进来的实际参数，传递给函数

fn.call([this],[param]....)

### call中的细节

1.非严格模式

如果不传递参数，或者第一个参数是Null或者是undefined，this都指向window

2.严格模式

第一个参数是谁，this就指向谁，包括null和undefined，如果不传参数this就是undefined

## apply

apply:方法基本和call一只，唯一区别在于传参的方式

apply把需要传递给fn的参数放到一个数组（或者类数组）中传递进去，虽然写的是一个数组，但是也相当于给fn一个个的传递

## bind

bind：语法和call一模一样，区别在于Bind改变了指向之后并不会立刻执行函数，需要手动执行，另外bind并不兼容IE6-8

```js
Function.prototype.myBind = function (context){
    if(typeof this !== 'function'){
        throw new TypeError('Error')
    }
    //返回一个绑定this的函数这里我们需要保存this
    const _this = this
    cosnt args =[...arguments].slice(1);
    return function F(){
        //因为返回一个函数，我们可以new F()需要判断是否能够做构造函数吗  
    	if(this instanceof F){
            return new _this(...args,...arguments)
        }
        return _this.apply(context,args.concat(...arguments))
    }
}
```



## Scroll事件的优化

scroll时间如果不做优化，默认情况下会频繁地被触发，如果在事件处理程序内进行了复杂的DOM操作，就会大大增加浏览器的负担，消耗性能。防抖和节流是能过有效优化这种事件的频繁发生的。

在绑定 scroll 、resize 这类事件时，当它发生时，**它被触发的频次非常高，间隔很近。如果事件中涉及到大量的位置计算、DOM 操作、元素重绘等工作且这些工作无法在下一个 scroll 事件触发前完成，就会造成浏览器掉帧。**加之用户鼠标滚动往往是连续的，就会持续触发 scroll 事件导致掉帧扩大、浏览器 CPU 使用率增加、用户体验受到影响。

