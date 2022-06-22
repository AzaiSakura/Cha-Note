## requestAnimationFrame详解

提到这个是因为一道题：用js实现一个无限循环的动画

首先想到的是定时器

```html
<!doctype html>
<html lang="en">
<head>
    <title>Document</title>
    <style>
        #e{
            width: 100px;
            height: 100px;
            background: red;
            position: absolute;
            left: 0;
            top: 0;
            zoom: 1;
        }
    </style>
</head>
<body>
<div id="e"></div>
<script>


    var e = document.getElementById("e");
    var flag = true;
    var left = 0;

    function render() {
        if(flag == true){
            if(left>=100){
                flag = false
            }
            e.style.left = ` ${left++}px`
        }else{
            if(left<=0){
                flag = true
            }
            e.style.left = ` ${left--}px`
        }
    }
    //这里使用定时器来实现渲染，为什么是1000/60，因为大多数首屏渲染的时间间隔都是每秒60帧
    //使用定时器效果
    setInterval(function(){
         render()
    },1000/60)
    //requestAnimationFrame效果
    (function animloop() {
        render();
        window.requestAnimationFrame(animloop);
    })();

</script>
</body>
</html>
```

## requestAnimationFrame比起setTimeout、setInterval的优势：

#### 1、requestAnimationFrame会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或者回流时间间隔紧紧跟随浏览器的刷新频率，这个频率一般是60帧每秒

#### 2、在隐藏或者不可见的元素中，requestAnimationFrameb将不会进行重绘或回流，当然就意味着更少的cpu、 gpu 和内存的使用量。



### 停止requestAnimationFrame的方法：

cancelAnimationFrame(id) ;这个API接收一个参数id

requestAnimationFrame默认返回一个id，cancelAnimationFrame只需要传入这个id就可以停止了。

### 修改requestAnimationFrame执行间隔方式：

默认情况下，requestAnimationFrame的执行频率默认是1000/60，大概16ms执行一次。

#### 如果我们现在想要每50ms执行一次怎么办呢？

requestAnimationFrame执行条件类似递归调用 （说的是类似）别咬我，既然这样的话我们能否自定一个时间间隔再执行呢？当然定时器这么low的东西我们就不考虑了，都已经抛弃它用rAF了， 这个思路来源于我几年前搞IM的一个项目，服务端推送消息为了减小包的大小不给时间戳，这个我们做前端的都知道，我们虽然很牛逼 不过用户更牛逼，万一改了时间就不好玩了。

解决方案是 当和服务端通信时 记录下一个时间差，（时间差等于服务端时间-本地时间）不管正负我们只要这个时间差。这样每当我们接受到消息 或者发送消息的时候我们就拿本地时间和是价差相加。这样就可以保证和服务端时间是一致的了，思路是不是很牛逼哈哈。

```js


    var e = document.getElementById("e");
    var flag = true;
    var left = 0;

     //当前执行时间
     var nowTime = 0;
    //记录每次动画执行结束的时间
    var lastTime = Date.now();
    //我们自己定义的动画时间差值
    var diffTime = 40;
    function render() {
        if(flag == true){
            if(left>=100){
                flag = false
            }
            e.style.left = ` ${left++}px`
        }else{
            if(left<=0){
                flag = true
            }
            e.style.left = ` ${left--}px`
        }
    }
    //requestAnimationFrame效果
    (function animloop() {
        //记录当前时间
        nowTime = Date.now()
        // 当前时间-上次执行时间如果大于diffTime，那么执行动画，并更新上次执行时间
        if(nowTime-lastTime > diffTime){
            lastTime = nowTime
            render();
        }
        requestAnimationFrame(animloop);

    })()
```

