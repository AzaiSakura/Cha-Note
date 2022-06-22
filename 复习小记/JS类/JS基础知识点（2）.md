## JS基础知识点（2）：防抖与节流、原型链、类与继承、Promise、回调地狱、this的理解、bind call apply、



## 防抖节流：

### 防抖：用户触发事件过于频繁，只要最后一次事件的操作

![image-20211130174447106](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211130174447106.png)

可以看到这样会频繁的触发事件，如果这每次事件的触发都是跟后端的交互，那么效率可想而知很低。

```html
//实现防抖
    <input type="text"> 
    <script>
        let inp = document.querySelector('input');
        let t =null;
        inp.oninput = function (){
            if(t !== null){
                clearTimeout(t);
            }
            t = setTimeout(()=>{
                console.log(this.value)
            },500)
        }
    </script>
```

可以通过定时器的方式来实现防抖， 逻辑：设置一个计时器，隔段时间获取到this.value的值，定时器时间一到 就会让t不在等于null就会清楚计时器t，那么在下一次事件再次出发的时候，判断t又会回到先从null开始。

### 利用闭包封装防抖代码：

```js
//多次点击 最后一次点击之后才开始调用函数
function debounce(fn,delay){
    let t =null;//因为是闭包内部的变量，在函数进行完毕时候就会被释放内存
    return function(){
        if(t !== null){
            clearTimeout(t);
        }
        t =setTimeout(()=>{
            fn().call(this);//此处为真正的逻辑代码
        },delay)
    }
}
//这样的话fn也不能是一个箭头函数，因为箭头函数无法用通过call改变this指向
```

```js
//立即执行的防抖，多次点击之后 从开始就立即开始执行fn了
function debounce(fn,delay){
    let t = null ; 
    return function(){
        if(t !== null) clearTimeout(t)
        if(t == null) fn.call(this)
        t = setTimeout(()=>{
            t = null;
        },delay);
    }
}
```



### 节流：控制执行次数（限制事件一定时间内触发次数）

```js
let flag = true ;
window.onscroll = function(){
if(flag){
	setTimeout(()=>{
		console.log('hello world');
		flag = true;
	},500)
}
	flag = false
}
```

## 

## 回调地狱：

![image-20211130212939975](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211130212939975.png)

这里要处理异步程序就需要回调，一回调就要嵌套，这样导致于程序内部越调用越多，让程序的可维护性变得非常非常差，这就是**回调地狱**。

ES6之前都是用回调解决异步的，为了解决上述问题于是ES6推出了promise

## Promise

```js
//promise对象,resolve可以将异步数据传出来
let p = new Promise((resolve,reject)=>{})
//通过then拿到resolve传出来的异步数据
p.then(function(data)){
       console.log(data)
       }
```

### promise链式调用

```js
 <script>
         function getTea(){
             return new Promise(function(resolve){
                 setTimeout(()=>{
                     resolve("奶茶")
                 },1000)
             })
         }

         function getHotpot(){
            return new Promise(function(resolve){
                 setTimeout(()=>{
                     resolve("火锅")
                 },2000)
             })
         }
//这样的链式调用方式就解决了回调地狱的问题
         getHotpot().then(function(data){
            console.log(data);
            return getTea()
         }).then(function(data){
             console.log(data);
         })
   </script>
```

虽然链式调用解决了回调地狱的问题，但如果链式调用多了其实看起来还是不是很优雅。

于是有了async函数

### Async函数：只是Promise的一种语法糖

```js
 async function getData(){
             //直接获取resolve传递出来的异步数据
             let hotpot = await getHotpot();
             console.log(hotpot);
             let tea = await getTea();
             console.log(tea);
         }
  getData();
//这样就不用像上述链式调用那样了，而且显得更像同步代码，使得代码更加优雅
```



## 原型与原型链

任何一个对象都有一个原型对象，他可以使用自己原型对象上面的所有属性和方法

```js
function Parent(name,age){
    this.name = name;
    this.age = age;
}
function Child(){
    Parent.apply(this);
}
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;
let p = new Parent();
let c = new Child();
```



prototype 原型对象  __proto\_\_ 原型

```js
  let cat ={
           name:"猫猫"
       }
       console.log(cat);
       cat.__proto__.eat  = function (){
           console.log("吃鱼");
       }
       cat.eat();
//给猫猫加上了吃鱼这个方法
```

获取原型的方法：

1.通过对象的__proto\_\_ 获取

2.通过构造函数的prototype属性拿到原型

3.通过类的prototype拿到原型

```js
//面试题： 构造函数的this指向谁
function Cat(name,age){
    this.name = name;
    this.age = age;
}
let cat = new Cat();
//指向new创建的对象
Cat.prototype.eat = function (){
    console.log("吃鱼");
}
cat.eat();
//这种就是通过构造函数的prototype设置原型
```

### ES6中的类：

```js
class Cat {
            constructor(name,age){
                this.name = name ;
                this.age = age;
            }
        }
        let cat = new Cat("猫猫","1");
        Cat.prototype.eat = function (){
            console.log("吃鱼");
        }

        console.log(cat);
        cat.eat();
//输出Cat{猫猫,1}  吃鱼

```

ES5通过构造函数创建对象，ES6通过类来创建

### 继承：

```js
class User{
         constructor(username,password){
             this.username = username;
             this.password = password
         }
         login (){
             console.log("登录");
         }
     }

     class Admin extends User{
         deletePerson(){
             console.log("删除一个人");
         }
     }
     let admin = new Admin();
     admin.login()
//能够输出登录
```

ES5中就可以通过原型指向来实现模拟继承，而ES6中的class可以通过 extends关键词实现继承（学java的）

### 原型链继承：

```js
function User(username,password){
         this.username = username;
         this.password = password;
         this.login = function (){
             console.log("登录");
         }
     }
     function Admin(){
         this.deletePerson = function (){
             console.log("删除一个人");
         }
     }
     Admin.prototype = new User();
     let admin = new Admin();
     admin.login();
//输出 登录
```

这里是我让admin的原型指向了 User的原型，如果我让User的原型在指向Obejct的原型，那么admin依然可以使用Object的方法。 这样就形成了一条原型链

最上层对象构造函数就是Object();

## new关键字：

new关键字会进行如下操作：

1.创建一个空的简单js对象（｛｝）；

2.将创建的对象添加属性__ proto __ 将该属性链接至构造函数的原型对象

3.将步骤1新创建的对象作为this的上下文；

4.如果该函数没有返回对象，那么则返回this；

```js
function Mynew(){
    let obj ={};
    Con(Constructor).letCon = [].shift.call(arguments);
    obj.__proto__ =  Con.prototype;
    let res = Con.apply(obj,arguments);
    return typeof res === 'object'?res:obj;
}
```



##  this关键字：

### this的指向：

1.直接输出this指向全局对象 ，全局对象（Window）

2.this放在方法中，this指向调用这个方法的对象

### 构造函数this指向   new关键字做了什么

```js
function F(){
         console.log("我是构造函数");
         this.name = 'xiaoming'
     }
     let f = new F();
     console.log(f);
//  new 关键字 创建了 一个对象 将构造函数中的this 指向创建出来的对象，此时就是f
```

箭头函数中没有this，所以箭头函数中this指向并不会改变



## Call、bind、apply

他们三个使用的时候是一模一样的。

call可以调用函数，call可以改变函数中的this

```js
function fun(){
  	console.log(this.name);
}
let cat ={
	name:"猫猫"
}
fun.call(cat)
//这样就可以让函数中的this指向cat对象
```

```js
 let dog ={
        say(){
            console.log("我是"+this.name);
        }  
        ,
        eat(food){
            console.log("我喜欢吃"+food);
        }
      }
      let cat = {
        name: "猫猫",
        
      };
      dog.eat.call(cat,"鱼")
//我喜欢吃鱼，call的后面的参数就是eat方法所需要的参数
```

![image-20211201201639924](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211201201639924.png)

### 基于call实现继承：

```js
 function Animal(){
          this.eat = function (){
              console.log("吃东西");
          }
      }
      function Cat(){
          //这里的this指向小cat,构造函数的this指向他创建的那个实例对象
          //所以这里就是把Animal的this改成指向cat这样就实现了一种继承
            Animal.call(this)
      }
      let cat = new Cat();
      cat.eat();
//在构造函数Cat中 让call改变Animal指向实现继承

```

