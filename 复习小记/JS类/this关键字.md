## this关键词

### this值

当前执行上下文（global/ function / 或者 eval）的一个属性，**在非严格模式下 ，总是指向一个对象，在严格模式下可以是任意值。**

## 全局上下文

无论是否在严格模式下，**在全局执行环境中（在任何函数体外部）this都是会指向全局对象的**。

```js
//在浏览器中，window对象同时也是全局对象
console.log(this === window)// true;
a = 37;
console.log(window.a); //37

```

## 函数上下文

**在函数内部，this的值取决于函数被调用的方式**

在非严格模式下。且this的值不是由该调用设置的，所以this默认指向全局对象。在浏览器中就是window

```js
function f1(){
    return this;
}
//浏览器
f1() === window //true；在浏览器中,全局对象是window
//Node
f1() === globalThis //在Node环境中全局对象就是当前export出去的组件
```

然而在严格模式下，如果进入执行环境时没有设置this的值，this会保持为undefined如下

```js
function f2(){
    "use strict";
    return this;
}

f2() === undefined;//true
```

如果要想把this的值从一个环境传到另一个，就要用call或者apply方法，如下方的示例所示。

## 类上下文

this在类中的表现与在函数中类似，因为类本质上也是函数，但也有一些区别和注意事项。

在类的构造函数中，this是一个常规对象。**类中所有非静态的方法都会被添加到this的原型中：**

```js
class Example{
    constructor() {
	const proto = Object.getProtypeOf(this);
     console.log(Object.getOwnPropertyNames(proto))  
	}
    fist(){}
    second(){}
    static third (){}
}
new Example();//['consturctor','first','second']
```

