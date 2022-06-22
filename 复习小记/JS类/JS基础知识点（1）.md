## JS基础知识点（1）：   let/const 、解构赋值、箭头函数、模板字符串、闭包

### let 、 var、const

let  const 有块级作用域 (不会造成全局变量污染) var没有，其次var还有**声明提升**

### 什么时候用let 什么时候用const： 

比如是 函数或者是对象或者是不变常量的时候可以是const

其他的计算啊 有进行赋值计算的啊就需要使用let了

#### let const 具有var不具有的块级作用域

JS作用域中有：全局作用域、函数作用域，在ES6之前并没有块级作用域这一概念。**块级作用域由{}包括，if语句和for语句里面的｛｝也属于块级作用域**

```js
{
    var a = 1;
    console.log(a);//1
}
console.log(a)//a
//可见，通过var定义的变量可以跨块作用域访问到

(function A(){
	var b =2;
    console.log(b);//2
})();
console.log(b)//报错
//可见，通过var定义的变量不能跨函数作用域访问到

if(true){
    var c =3 ;
}
console.log(c);//3
for(var i = 0 ; i <4;i++){
	var d=5
}
console.log(i);//4  循环结束i=4 所以这里是4
console.log(d) // 5 
//if语句和for语句中var定义的变量可以在外面访问到
//可见，if语句和for语句属于块作用域，不属于函数作用域
```

```js
{
    var a=1;
    let b=2;
    const c =3 ;
    c=4 //报错 const 用于定义常量不能修改，除非是对象或数组
    var aa ;
    let bb ;
    const cc ;//报错 必须要在声明时赋值
    console.log(a);//1
    console.log(b);//2
    console.log(c);//3
    console.log(aa);//undefined
    console.log(bb);//undefined
}
console.log(a);//1
console.log(b);//报错
console.log(c);//报错 
//这里就是块级作用域

//函数作用域
(function A(){
    var d= 5;
    let e =6;
    const f =7;
    console.log(d);//5
    console.log(e);//6 在同一个{}中，也属于一个块，可以正常访问到
    console.log(f);//7
})
console.log(d);//报错
console.log(e);//报错
console.log(f);//报错
```

**允许块级作用域内声明函数**

**函数声明类似var，即会提升到全局作用于或者函数作用与的头部**

### 同时，函数声明还会提升到所在块级作用域的头部。

另外let const还有**暂时性死区**

```js
a = 10;
let a;
这样会报错
a=10;
var a;
console.log(a);//这样会输出10
```



```js
for(var i =0 ;i<4;i++){
    console.log(i); 
} // 0  1 2 3
for(let i =0 ;i <4;i++){
    console.log(i);
}// 0 1 2 3
```

```js
//当涉及到闭包时
for(var i=0;i<4;i++){
    setTimeout(()=>{
	console.log(i);
    })
}// 4 4 4 4
//这是因为setTimeout是异步任务，会在同步的for执行完之后才执行，var的话这里涉及一个闭包，在定时器这个作用域内没有i所以需要向父级元素里面寻找，当找到i时就是for循环循环完毕之时，此时的i为4所以在开始执行这4个定时器，此时输出四个4

解决方法1：
for(let i=0;i<4;i++){
    setTimeout(()=>{
	console.log(i);
    })
}// 0  1 2 3;  而这里输出0 1 2 3是因为let的块级作用域，能绑定死每次循环时候的定时器对应的那个i值。
解决方法2
for(var i = 0 ;i<4;i++){
    //通过立即执行函数传递变量i来达到每个函数可以获取到正确i的效果
    setTimeout((j)=>{
        return function (){
            console.log(j)
        }
    }(i),1000);
}
```



### 解构赋值：

数组的结构赋值用的比较少，对象的解构赋值比较多

![image-20211130152409649](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211130152409649.png)

### 解构赋值的意义：当实际开发中我们并不关心某个对象其他的值，只是关心他其中某项或者某几项的值，这个时候使用解构赋值直接获取或者更改对应的值即可而无需操作对象，使得代码更加清爽和优雅。



## 箭头函数：

```js
(a,b) =>{return;}  //这就是箭头函数归根结底他就是一个函数
```

只有一个参数的时候甚至可以去掉 ()  只有一行函数语句的时候可以省略{}

如果返回值只有一个那么可以不写return

### 箭头函数的this指向：

普通函数：谁调用这个函数，那么this就指向谁

箭头函数：谁在哪里定义的函数，this就指向谁

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211201142143960.png" alt="image-20211201142143960" style="zoom:67%;" />



## 模板字符串：

转移符\

用反引号来表示模板字符串

```
let = `芜湖'起'飞`
```

取缔了以前的+号的字符串链接

这种反引号的方式不仅很方便，而且支持反引号的内容换号。

![image-20211130162523288](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211130162523288.png)

## 闭包：

闭包的本质就是将函数内部和函数外部连接起来的桥梁;让内部函数能够使用到外部函数的变量。

**一个函数和对其周围环境状态（词法环境）的引用捆绑在一起**，这样的组合就是**闭包**，也就是说，闭包让我们可以在一个内层函数中访问到**其外层函数的作用域**。

### 闭包的作用：

#### 1.存储变量

```js
function s(){
    var a = 1 ; 
    return function(){
        return a ;
    }
}
var d = s();
console.log(d());
//内部函数获取到外部函数的变量a

```

#### 2.封装私有变量

```js
var person = function(){
    var name = 'defalut';
    return {
        getName:function (){
            return name ;
        }
        setName:function (newName){
            name = newName;
        }
    }
}
```



可以看到，里面函数是拿不到外面声明的函数的。

```js
function outer(){
    let a =10;
    function inner(){
        console.log(a)
    }
    return inner;
}
outer();//10
```



听过闭包，把闭包函数Inner作为return返回值，这样就可以拿到上层的a的值。（正常情况下，执行的时候是可以拿到的**，当执行完后这个变量就会被释放内存空间（销毁）**，这也是为什么在函数内部是无法调用这个a的）



### 闭包特性所带来的一些问题：内部函数没有执行完成，外部函数变量不会被销毁。就可能会导致内存泄露

也正是因为如此，闭包在项目中要避免大量使用，因为闭包中的变量不会回收，这样随着变量数量的增加，内存的消耗也不断变大，最后可能出现内存泄露，导致页面假死，浏览器崩溃的问题。

另外在ECMA2015引入let关键字之前，在循环中还有一个常见的闭包问题：

```html
<p id="help">Helpful notes will appear here</p>
<p>E-mail: <input type="text" id="email" name="email"></p>
<p>Name: <input type="text" id="name" name="name"></p>
<p>Age: <input type="text" id="age" name="age"></p>
```

```js
function showHelp(help) {
  document.getElementById('help').innerHTML = help;
}

function setupHelp() {
  var helpText = [
      {'id': 'email', 'help': 'Your e-mail address'},
      {'id': 'name', 'help': 'Your full name'},
      {'id': 'age', 'help': 'Your age (you must be over 16)'}
    ];

  for (var i = 0; i < helpText.length; i++) {
    var item = helpText[i];
      //下面是闭包部分
    document.getElementById(item.id).onfocus = function() {
      showHelp(item.help);
    }
  }
}
setupHelp();
```

helpText中有a那个有用的提示信息，通过每一个都关联与对应文档中input的id，通过循环这三项的定义，依次为相应的input添加了一个onfocus时间来处理函数以便于帮助显示，但是运行后我们会发现无论焦点放在哪个input上面，都会提示年龄相关的信息。

原因是**这里的复制给onfocus的是闭包**。这些闭包是由他们的函数定义和在setupHelp()作用域中捕获的环境所组成，这三个闭包在循环中被构建，但是他们共享了同一个词法作用域，在这个作用域中存在一个被他们三引用的变量item。**这是因为变量item使用var声明，让变量提升，所以具有函数作用域。当onfocus的回调执行时候，item.help的值被决定。由于循环在事件触发之前早已经执行完毕，变量和item已经指向了helpText的最后一项。**

### 解决方案：

#### 1使用更多的闭包

```js
function makeHelpCallback(help) {
  return function() {
    showHelp(help);
  };
}
function setupHelp() {
  var helpText = [
      {'id': 'email', 'help': 'Your e-mail address'},
      {'id': 'name', 'help': 'Your full name'},
      {'id': 'age', 'help': 'Your age (you must be over 16)'}
    ];
  for (var i = 0; i < helpText.length; i++) {
    var item = helpText[i];
    document.getElementById(item.id).onfocus = makeHelpCallback(item.help);
  }
}
setupHelp();
//这段代码就可以如我们预期的那样去工作，所有的回调不再是共享一个环境，makeHelpCallback函数为每一个onfocus回调创建了一个新的词法环境。在这些环境中help指向helpText数组中对应的字符串
```

#### 2.使用匿名方法

```js
function showHelp(help) {
  document.getElementById('help').innerHTML = help;
}

function setupHelp() {
  var helpText = [
      {'id': 'email', 'help': 'Your e-mail address'},
      {'id': 'name', 'help': 'Your full name'},
      {'id': 'age', 'help': 'Your age (you must be over 16)'}
    ];
    (function() {
        var item  =helpText[i];
        document.getElementById(item.id).onfocus = fuction() {
            showHelp(item.help);
        }
    })(); //马上把当前循环项的item与事件回调相关联起来
}
setupHelp();
```

#### 3.使用let关键词

```js
function showHelp(help) {
  document.getElementById('help').innerHTML = help;
}

function setupHelp() {
  var helpText = [
      {'id': 'email', 'help': 'Your e-mail address'},
      {'id': 'name', 'help': 'Your full name'},
      {'id': 'age', 'help': 'Your age (you must be over 16)'}
    ];

  for (var i = 0; i < helpText.length; i++) {
    let item = helpText[i];
    document.getElementById(item.id).onfocus = function() {
      showHelp(item.help);
    }
  }
}

setupHelp();
```

这里例子里面使用到了let关键字而不是var，因此每个闭包都绑定了块级作用域的变量，这意味着不在需要额外的闭包。

### 闭包应用：1、闭包可以封装一段代码，实现模块化

```js
let module = (function (){
    let a =10;
    let b =20 ;
    function add(){
        return a+b;
    }
    return {add};
})();
console.log(module.add())
```

比如我们想在页面上添加一个调整字号的按钮，一种放马就是直接获取元素设置fontSize于是我们可以通过闭包来设置这种方式

```js
function makeSizer(size){
    return function(){
        document.body.style.fontSize = size +'px';
    }
}
```

### 2、利用闭包减少传递参数的参数量

```js
function add(x,y){
    return function(){
        	return x+y;
    }
}
add(x,y);
function KelihuaAdd(x){
    return function(y){
        return x+y;
    }
}
add(x)(y);
```

柯里化就是闭包减少传递参数的表现之一。

## 闭包的性能考量：

如果不是某些特定人物需要使用闭包，在其他函数中创建函数是不明智的，因为闭包在处理速度和内存消耗方面对脚本性能具有负面影响。

例如，在创建新的对象或者类时，方法通常关联与对象的原型，而不是定义到对象的构造器中，原因是这将导致每次构造器被调用时，方法都会被重新赋值一次。



### 作用域链：

```js
let a =1;
function outer(){
    function inner(){
       function inner2(){
            console.log(a);
       }
        return inner2;
    }
    return inner;
}
outer()()();//1
//在同一个词法作用域中，inner2中没有a所以往上一层寻找变量a，然后去到Inner函数作用域里面找也没找到，知道直到了outer函数与a变量构成的一个词法作用域中找到了a并且调用，
//这个寻找的过程就形成了一条作用域链。
```



```js
let a =1; 
function outer(){
    let a = 2; 
    console.log(a);//2
    function inner(){
        console.log(a);//2
    }
    inner();
}
console.log(a);//1
```

像上面那种，第一个2是因为在outer的函数作用域已经有了声明变量2.所以能拿到a=2，

**inner中输出2是因为inner在声明的时候与outer内部的局部变量创造的同一词法作用域构成闭包**，由于是所以inner中没有找到a就沿着作用域链往上一层寻找变量a，第三个a=1是因为全局有a=1变量

```js

function b(){
    let c =3;
    var e =4 ;
}
b();
function d(){
    console.log(c);//ReferenceError 拿不到 垮不了函数作用域
    console.log(e);//ReferenceError 拿不到 垮不了函数作用域
}
d();
//这里因为b函数和d函数是两个不同的函数作用域，所以不能跨过函数作用域拿到对方的变量
```

当我们函数内部有变量a的时候外面也有全局变量a的时候，此时会优先选择我们函数内部的这个值，如果内部没有才会到上层寻找，这个向上找的过程就可以变成一波作用域链，**需要和闭包区别的是闭包使用外部函数变量时是用let声明的，let具有块级作用域。**

