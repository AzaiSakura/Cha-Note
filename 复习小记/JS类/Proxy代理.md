## 	Proxy代理

```
let proxy = new Proxy(target,handler)
```

target 需要拦截代理的目标对象

handler一个对象表示用来定制拦截行为

![image-20211208095448962](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211208095448962.png)

```js
let obj = {
            name:'achacha',
            age:24
        }
        let objProxy = new Proxy(obj,{
            get:function(target,key){
                console.log(target,key);
                //这里的target是obj对象,key是调用哪个属性就返回哪个属性这里是name
                return target[key]
            }
        })
        console.log(objProxy.name);
		//这里返回的就是上面get的return值 就是obj的name属性
```

如果访问目标属性不存在的话。希望能抛出一个错误（加个判断就行了）

如果没有代理/拦截操作，都会返回一个undefined



## Reflect

Reflect是ES6中为了操作对象引入的新的API

Reflect并非一个构造函数，所以不能通过new运算符来对齐进行调用（ES6自带的对象）

Reflect**对象**是一个全局的普通对象，它所有的属性和方法都是静态的。一共有13种静态方法

![image-20220302125816233](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220302125816233.png)

## Reflect的设计目的：

1、将Object对象的一些明显属于语言内部的方法（比如Object.defineProperty），放到Relfect对象上

2、修改某些Object方法的返回结果让其变得更加合适

```js
var obj = {
    name: '阿猹猹',
    job: '学生',
    get age(){
        return '起飞咯'
    }
}

console.log(Reflect.get(obj,'name'))//阿猹猹
console.log(Reflect.get(obj,'job'))//学生
```

在Vue2中我们是通过一个Object.property去截取目标对象属性的变动的。在Vue3中，Vue采用了ES6中的proxy函数来实现响应式，当我们从一个组件的data函数中返回了一个普通的js对象时候，vue会将该对象包裹在一个带有get和set的Proxy函数对象中，但是Proxy对象是一个对象，它还包装了另外一个对象Reflect,并且允许你**拦截**对该对象的**任何交互**。（Proxy对象是一个代理，真正去处理和拦截的是Reflect对象）

**Proxy的一个难点在于this的绑定，**我们希望任何方法都绑定到这个Proxy而不是目标对象，这样我们也可以拦截他们。Reflect的新特性就能够帮助我们以最小的代价消除这个问题。

```js
const dinner = {
  meal: 'tacos'
}

const handler = {
  get(target, property, receiver) {
    return Reflect.get(...arguments)
  }
}

const proxy = new Proxy(dinner, handler)
console.log(proxy.meal)//tacos

```



Proxy让我们能够截取目标对象property的举动，像这样的处理函数也称为一个捕捉器，有许多可用的不同类型的捕捉器，每个都能够出不同类型的交互。

除了控制台日志，我们可以在这里做任何我们想做的事情，如果我们愿意甚至可以不返回实际值，这就是Proxy的API的强大



