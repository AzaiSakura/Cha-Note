## CSS复习遗忘点（2）

CSS像素：在CSS中的抽象单位

物理像素：只与设备硬件密度有关，任何设备的物理像素都是固定的。

em 和rem 相对于px更加灵活，他们都是想对长度单位，他们之间的区别是：

em相对于父元素  rem相对于根元素

vw/vh ：是与视图有关的单位，vw表示视图窗口的宽度，vh表示视图窗口的高度，除了vh和vm外还有vmin vmax

vmin: vw和 vh中较小值

vmax： vw和vh中的较大值

**vw/vh** 和百分比很类似，两者的区别：

- 百分比（`%`）：大部分相对于祖先元素，也有相对于自身的情况比如（border-radius、translate等)

- vw/vm：相对于视窗的尺寸

### 

## BFC：块级格式化上下文

BFC简单来说就是一个完全独立的空间（布局环境），**让空间里面的子元素不会影响到外面的布局，**那么怎么使用BFC呢，BFC好似一个CSS元素属性

我们可以使用以下方式来触发BFC：

​		1、overflow ：hidden

​		2、display ：inline-block

​		3、position ： absolute

​        4、 position  :  fixed

​		5、display :flex

​		6/ display : table-cell

​		7、float：  浮动也可以激活bfc 只要不是默认的None就行

### 	在同一个BFC下，margin会发生折叠（margin重叠问题）如果想要他们不折叠，可以考虑将他们放入不同BFC容器中

```
<head>
div{
    width: 100px;
    height: 100px;
    background: lightblue;
    margin: 100px;
}
</head>
<body>
    <div></div>
    <div></div>
</body>
```

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220218202141491.png" alt="image-20220218202141491" style="zoom: 67%;" />

### 使用不同BFC之后：

```
<div class="container">
    <p></p>
</div>
<div class="container">
    <p></p>
</div>

.container {
    overflow: hidden;
}
p {
    width: 100px;
    height: 100px;
    background: lightblue;
    margin: 100px;
}
//为他们的外部包裹容器div都设施overflow:hidden触发各自的bfc
```

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220218202218488.png" alt="image-20220218202218488" style="zoom:67%;" />



### flex布局：

### 容器：实现flex布局需要先指定一个容器，任何一个容器都可以被指定为flex容器，这样容器内部的元素就可以使用flex布局。

display: flex | inline-flex

**需要注意的是：当时设置flex布局之后，子元素的float、clear、vertical-align的属性将会失效。**

下面六中属性可以**设置在容器上**，他们分别是：

1.flex-direction  2.flex-wrap 3.flex-flow 4.justify-content 5.align-items 6.align-content

### 1.flex-direction:决定主轴的方向

row 默认值 主轴为水平方向 起点在左

row-reverse 起点在右

column 主轴为垂直方向起点在上沿

column-reverse 起点在下沿

### 2.flex-wrap：决定容器内项是否可以换行

默认情况下，项目都排在主轴线上，使用flex-wrap可实现项目换行

nowrap:不换行

wrap： 项目主轴尺寸超出容器时候换行，第一行在上方

wrap-reverse:换行，第一行字下方

### 3.flex-flow:   flex-direction和flex-wrap的简写

### 4.justify-content:定义了项目在主轴的对齐方式

flex-start 左对齐   flex-end 右对齐  center居中 

space-between 两端对齐  

space-around 每个项目两侧间隔相等

### 5.align-items:定义了项目在交叉轴上的对齐方式（元素排列）

默认值stretch即如果项目没设置高度或者为auto将占满整个容器高度

flex-start：交叉轴的起点对齐

flex-end：终点对齐

center：中点对齐

baseline：第一行文字的基线对齐

### 6.align-content：定义了多跟轴线对齐方式，如果项目只有一个轴线那么不起作用（内容排列）



### 子容器：

容器内的子容器可以设置的属性：

flex-grow:定义子元素的放大比例，默认为0，如果存在剩余空间也不放大

flex-shrink:定义缩小比例，默认为1

flex简写 简写默认三个值 flex-grow flex-shrink flex-basis

flex-basis:定义项目在分配多余的空间之前，项目占据的主轴空间，默认为auto

### flex：1     =  flex ： 1  1    0%

### flex:auto = flex:1 1 auto

### flex:0 = flex:0 1 0%;

### flex:none = flex:0 0 auto



新标准的display: flex 并不能够完全替代display: -webkit-box

![image-20211128202828668](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211128202828668.png)



## 高度坍塌的解决方案：

父元素高度坍塌的原因是因为父元素的高度是有子元素内容来撑开的，所以一旦我们让子元素脱离了文本流，那么父元素，就会变回里面没有内容的状态，导致**高度坍塌**，那么要解决这个问题所在，我们思路的出发点就有两个，

1、让父元素本身就有高度，即给父元素本身设置好高度

2、让父元素也触发BFC脱离文本流

3、使用after伪元素（推荐使用）来使用clear : both 属性 来消除浮动

```js
 .parent::after{
       height: 0px;
       line-height: 0px;
       clear: both;
       display: block;
       content: "";
     }
```





**脱离文档流特点**：1.块元素不在独占页面的一行

​                             2.脱离文档流之后，块元素的宽度和高度都默认被内容撑开

​                             3.行内元素设置浮动之后会变成块元素，特点和脱离的块元素一样且设置宽高

​                              4.脱离文档流之后就不在区分块和行内元素了。

### background属性：

background属性是对所有背景属性的一个总和化，都可以设置

```
包括由 background-color//背景颜色
	background-image//背景图片
	background-size//背景图片尺寸
	background-repeat//如何重复背景图像
	background-orgin//规定图片定位位置
	background-clip//规定图片的绘制区域
	background-position//规定背景图片位置
	background-attachment//规定图像是否固定或者随页面其余部分滚动
```

