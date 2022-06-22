CSS场景题

### 实现一个三角形：    transparent（透明的）

```css
div{
width:0;
height:0;
border-bottom:50px solid red;
border-right: 50px solid transparent;
border-left:50px solid transparent;
}
```

### 实现一个扇形：

```css
div{
    border:100px solid transparent;
 	width:0;
 	height:0;
 	border-radius:100px;
 	border-top-color:red;
}
```

### 画一条0.5px的线：

```css
transform： scale(0.5,0.5);
```

### 移动端1px的问题：

在一些Retina屏幕的机型上，移动端页面的1px会变得很粗，呈现出不止1px的效果。

解决方法：

对meta标签里面的几个关键属性下手

```html
<meta name="viewport" content="initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no">

```

这里针对像素比为2的页面，把整个页面缩放为了原来的二分之一，这样，本来占用两个物理像素的1px样式，现在占用的就是一个物理像素

js代码如下：

```js
const scale = 1 / window.devicePixelRatio;
// 这里 metaEl 指的是 meta 标签对应的 Dom
metaEl.setAttribute('content', `width=device-width,user-scalable=no,initial-scale=${scale},maximum-scale=${scale},minimum-scale=${scale}`);

```

这样解决了，但这样做的副作用也很大，整个页面被缩放了。这时 1px 已经被处理成物理像素大小，这样的大小在手机上显示边框很合适。但是，一些原本不需要被缩小的内容，比如文字、图片等，也被无差别缩小掉了。                             



## 常见布局：

### 两栏布局：

两栏布局一般指的高度固定，右边宽度自适应

1.利用浮动，将左边元素宽度设置为 200px，并且设置向左浮动。将右边元素的 margin-left 设置为 200px，宽度设置为 auto（默认为 auto，撑满整个父元素）

2.利用浮动，左侧元素设置固定大小，并左浮动，右侧元素设置 overflow: hidden; 这样右边就触发了 BFC，BFC 的区域不会与浮动元素发生重叠，所以两侧就不会发生重叠

3.利用 flex 布局，将左边元素设置为固定宽度 200px，将右边的元素设置为 flex:1。

### 九宫格布局：

```html
<body>
<div id="parent">
    <div class="row">
        <div class="item">1</div>
        <div class="item">2</div>
        <div class="item">3</div>
    </div>
    <div class="row">
        <div class="item">4</div>
        <div class="item">5</div>
        <div class="item">6</div>
    </div>
    <div class="row">
        <div class="item">7</div>
        <div class="item">8</div>
        <div class="item">9</div>
    </div>
</div>
</body>
```

```css
#parent {
    width: 300px;
    height: 300px;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
}
.row {
    display: flex;
    flex: 1;
}
.item {
    flex: 1;
    border: 1px solid #000;
}
```

### 全屏布局：

```html
<body>
<div id="parent">
    <div id="top">top</div>
    <div id="left">left</div>
    <div id="right">right</div>
    <div id="bottom">bottom</div>
</div>
</body>
```

```css
        html, body, #parent {height: 100%;overflow: hidden;}
        #parent{
            position: relative;
        }
        #parent > div{
            border: 1px solid black;
        }
        #top{
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 100px;
        }
        #left{
            position: absolute;
            top: 100px;
            left: 0;
            bottom: 50px;
            width: 200px;
        }
        #right{
            position: absolute;
            top: 100px;
            left: 200px;
            right: 0;
            bottom: 50px;
            overflow: auto;
        }
        #bottom{
            position: absolute;
            left: 0;
            right: 0;
            bottom: 0;
            height: 50px;
        }
      * {
        margin: 0;
        padding: 0;
      }
```



### 圣杯布局：

```html
 <div class="container">
      <div class="middle"></div>
      <div class="left">left</div>
      <div class="right">right</div>
  </div>
```

```css
.container{
  padding: 0 200px;
 
}
.middle{
  width: 100%;
  background: paleturquoise;
  height: 200px;
  float: left;
}
.left{
  background: palevioletred;
  width: 200px;
  height: 200px;
  float: left;
  font-size: 40px;
  color: #fff;
  margin-left: -100% ;
  position: relative;
  left: -200px;
}
.right{
  width: 200px;
  height: 200px;
  background: purple;
  font-size: 40px;
  float: left;
  color: #fff;
  margin-left:-200px;
  position: relative;
  right: -200px;
}
      * {
        margin: 0;
        padding: 0;
      }
```

