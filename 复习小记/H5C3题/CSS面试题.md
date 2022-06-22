## CSS面试题

### 1.水平垂直居中

#### 定宽高：

1.绝对定位和负margin（margin设置值为子元素的宽高的一半）

```css
<style>
    body{
        margin: 0;
        padding: 0;
    }
    #parent{
        width: 100vw;
        height: 100vh;
        background-color: #999;
        overflow: hidden;
    }
    #children{
        width: 50px;
        height: 50px;
        background-color: red;
        transform: translate(50vw,50vh);
        margin-top: -25px;
        margin-left: -25px;
    }
</style>

```



2.绝对定位加transform

![image-20211129143143177](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211129143143177.png)

3.绝对定位加bottom,right,top.left,+margin

```css
<style>
    body{
        margin: 0;
        padding: 0;
    }
    #parent{
        width: 100vw;
        height: 100vh;
        background-color: #999;
        position: relative;
    }
    #children{
        width: 50px;
        height: 50px;
        background-color: red;
        position: absolute;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        margin: auto;
    }
</style>

```

4.flex布局+justify-content+align-items

5.子元素设置display:inline-block 父元素设置text-aligin:center且line-height == height

```css
<style>
    body{
        margin: 0;
        padding: 0;
    }
    #parent{
        width: 100vw;
        height: 100vh;
        line-height: 100vh;
        background-color: #999;
        text-align: center;
    }
    #children{
        width: 50px;
        height: 50px;
        background-color: red;
        display: inline-block;
    }
</style>

```

#### 不定宽高：

1.绝对定位+transform 和上面一样

2.table-cell

![image-20211129143709995](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211129143709995.png)

3.flex布局 和上方一样

4.flex变异布局  display:flex + margin:auto

5.grid+flex布局  父元素开grid  子元素align-self+justify-self



### 行内元素的居中布局：

水平居中： 

行内元素可以设置 : text-align:center

flex 布局设置父元素： display:flex  ;justify-content:center

垂直居中：

单行文本元素确认高度： height ===line-height

多行文本父元素确定高度：display：table-cell ；vertical-align：middle

### 块级元素居中布局

水平居中

- 定宽: margin: 0 auto; 
- 不定宽： 参考上诉例子中不定宽高例子。 

垂直居中

- position: absolute 设置 left、top、margin-left、margin-top(定高)； 
- position: fixed 设置 margin: auto(定高)； 
- display: table-cell； 
- transform: translate(x, y)； 
- flex(不定高，不定宽)； 
- grid(不定高，不定宽)，兼容性相对比较差；

## 浮动：

### 浮动元素引起的问题：

父元素高度无法撑开，影响与父元素同级的元素

与浮动元素同级的非浮动元素会跟随其后

若浮动的元素不是第一个元素那么该元素之前的元素也要浮动，否则会影响页面显示结构。

### 清除浮动方式：

给父元素div设置高度

最后一个浮动元素之后加个空的div标签并添加clear:both

包含浮动元素的父级标签加入overflow:hidden 或者overflow:auto

使用:after伪元素

### BFC:

块级格式化上下文，是一个独立的渲染区域，并且有一定的布局规则。

BFC区域不会与float box重叠

BFC是页面上一个独立容器，子元素不会影响到外面

计算到BFC高度的时候，浮动元素也会参与计算

用于清除浮动防止margin重叠

哪些元素会生成 BFC：

- 根元素 
- float 不为 none 的元素 
- position 为 fixed 和 absolute 的元素 
- display 为 inline-block、table-cell、table-caption，flex，inline-flex 的元素 
- overflow 不为 visible 的元素

## 定位：

![image-20211129150413127](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211129150413127.png)

### offset/scroll/client 各类属性

- clientHeight：表示的是可视区域的高度，不包含 border 和滚动条 
- offsetHeight：表示可视区域的高度，包含了 border 和滚动条 
- scrollHeight：表示了所有区域的高度，包含了因为滚动被隐藏的部分。 
- offsetheight = scrollheight + clientHeight
- clientTop：表示边框 border 的厚度，在未指定的情况下一般为 0 
- scrollTop：滚动后被隐藏的高度，获取对象相对于由 offsetParent 属性指定的父坐标(css 定位的元素或 body 元素)距离顶端的高度

![image-20211129150705906](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211129150705906.png)



### ![image-20211129150831001](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211129150831001.png)

