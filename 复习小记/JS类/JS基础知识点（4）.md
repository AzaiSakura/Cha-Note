## JS基础知识点（4）：事件委托、symbol、数组扁平化、暂时性死区、垃圾回收机制、图片懒加载、跨域、函数柯里化



## DOM事件流与事件委托：

事件的触发就会触发Dom事件流。**事件委托也称为事件代理，就是利用事件冒泡，把子元素的事件绑定到父元素上面。如果子元素阻止了事件冒泡，那么委托也无法实现。**

### 事件委托的原理：

不是每个子节点单独设置事件监听器，而是事件监听器设置在其父元素上面，然后利用冒泡的原理影响设置每个子节点。

```html
<body>
    <ul>
        <li>11111</li>
        <li>22222</li>
        <li>33333</li>
        <li>44444</li>
    </ul>
</body>
需求：我们想要点击某个li高亮
<script>
	let li =document.querySelectorAll('li')
    for(var i = 0; i<li.length; i++){
        li[i].onclick = function (){
            this.style.color = 'green';
        }
    }
   //这种方式能够完成需求但是访问DOM次数很多，会延长整个页面的交互就绪时间
    //所以我们可以使用到事件委托来帮助我们完成事件
    //这样我们就可以利用到事件对象target来找到当前点击的li然后为其触发事件监听器，这样我们只用访问一次DOM就能完成需求，大大提升了性能。
    let ul = document.querySelector('ul')
    ul.addEventListener('click',function (item){
        item.target.style.color = 'orange';
    })
</script>
```



### 事件冒泡和事件捕获：

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211202144313604.png" alt="image-20211202144313604" style="zoom: 50%;" />

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211202144332180.png" alt="image-20211202144332180" style="zoom:80%;" />

一般来讲事件委托是在冒泡阶段触发的，所以我们点击small会触发了small又会触发了center和big

通过addEventListner(事件名，cb，boolean)方法的第三个参数可以事件捕获还是冒泡,默认是false

是事件冒泡形式

### 事件委托：子级事件交给父级进来处理

obj.target属性能捕获到对应的事件的元素

### 取消冒泡的几种方式

**w3c的方法是e.stopPropagation()，IE则是使用e.cancelBubble = true**

#### 1.event.stopPropagation() 

这是阻止事件冒泡的方法，不让事件向document上蔓延，但是默认事件任然会执行，当你调用这个方法的时候，如果点击一个连接，这个连接仍然会被打开

#### 2.event.preventDefault方法

这是阻止默认事件的方法，调用该方法，链接不会被打开，但是会发生冒泡，冒泡会传递到上一层父元素

#### 3.return false

```js
$(".btn").click(function (even){
    alert('按钮被点击了');
    return false;
})
```

这个方法比较暴力，他会同时组织时间冒泡也会阻止默认事件的发生

## Symbol 第七种基本数据类型：

js七种数据类型： number  string  boolean  obj  null   undefined symbol

symbol可以当做对象的属性名

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211202145729225.png" alt="image-20211202145729225" style="zoom:50%;" />

而且需要获取level变量就是 student[level]而不是用 . level

用变量作为属性名的时候需要用括号括住。

symbol属性不能被for .. in 获取

并且Object.keys(object)也不能取到symbol变量属性

通过Object.getOwnPropertySymbols()可以获取到对应的symbol属性，在通过for of 就能拿到symbol属性的值了

js中有些内置的symbol属性比如Symbol.iterator是一个内置的值

如果对象有Symbol.iterator属性就可以被for..of遍历

## 

## 图片懒加载：

```
const observer =new IntersectionObserver（cb，..）
```

来实现观察视口，

![image-20211203212844655](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211203212844655.png)



## 跨域：

由于浏览器的同源策略，才使得一个域不能跨到另外一个域

![image-20211203213320746](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211203213320746.png)

就算端口号不同也是需要跨域的

但是script标签是允许请求外部域的资源，这就是jsonp的原理

由于html5 script标签的默认type是text/javascript 所以导致请求的资源会被浏览器以js代码来执行

JSONP只支持get不支持post

### Cors:就是在头部orgin:协议+主机+端口设置为

![image-20211203214037183](C:\Users\11791\Desktop\前端\前端面试题\JS类\image-20211203214037183.png)

来实现跨域

### 服务器反向代理：nginx



## 函数柯里化：

**为了减少大量写入重复参数，利用好参数的复用**

![image-20211203214918810](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211203214918810.png)

柯里化也是闭包的一种应用体现之一

```js
// 柯里化之前
function add(x, y) {
  return x + y;
}

add(1, 2) // 3

// 柯里化之后
function addX(y) {
  return function (x) {
    return x + y;
  };
}

addX(2)(1) // 3
```

如上面所显示AddX是一个函数工厂，它创建了一个将指定的值和它的参数想加的函数，add和addX都是闭包。它们共享相同函数的函数定义，但是**保存了不同的词法环境**
