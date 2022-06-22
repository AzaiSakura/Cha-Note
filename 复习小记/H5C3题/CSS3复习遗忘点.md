## CSS3复习遗忘点

### em和rem:

em是相当于元素的字体大小来计算的会根据字体大小的变化而变化

​	1em = 1 font-size

rem： rem是相对于根元素（HTML）的字体大小来计算的



### 各类选择器及其优先级

行内样式选择器：1000   id选择器：100  伪类、属性、类：10    	!important也是最高优先级

**内联样式选择器> id选择器> 类和伪类选择器> 元素选择器> 通配选择器> 样式继承**



### 内联样式：直接在元素标签的style元素中设置css样式这种方式叫做内联样式



**样式继承：继承是发生在祖先后代之间的，继承的设计是为了方便我们开发利用继承一次设置就能多个使用，然后并非所有样式都能继承，与背景相关的布局相关的这类样式设置不会被继承**

**伪类**有：

​    ：first-chil  第一个**子元素**

​	:   last-child 最后一个子元素

​	:  nth-child(n) 第n个子元素 // 特殊值**n** 第n个   **2n或者even** 选中偶数位的子元素  **2n+1或者odd**选择奇数位的子元素

​	：not（）否定伪类  ： 将符合条件的元素从选择器中去除

**伪元素**：

​	：：first-letter  第一个字母

​	：：first-line 第一行

​	：：slection 表示选中内容

​	：：before  元素的开始位置

​	：：after 元素的最后位置

覆盖规则：

1.由于继承而发生样式冲突时，祖先获胜

2.继承样式和直接指定的样式冲突时候，直接指定样式获胜

3.直接指定的样式发生冲突时候，权值高获胜

4.权值相同，后者获胜

5.！importanrt的样式属性不会被覆盖

### CSS3新特性：

1.各种选择器 2.圆角（border-radius）3.多列布局（multi-column layout）4.阴影和反射（Shadowflect）5.文字特效（text-shadow） 6.文字渲染（Text-decoration）

7.线性渐变（gradient）8.旋转（transform）9.增加了选择，缩放定位，倾斜，动画多背景等。

### CSS动画实现：

创建动画序列，使用animation属性或者其子属性，该属性允许配置动画时间、时长以及其他动画细节，但是该属性不能配置动画的实际表现，动画的实际表现是由@keyframes规则实现的。transition也可以实现动画.transition强调过渡，是元素的一个或者多个属性发生变化时候的过渡效果，同一个元素通过两个不同途径获取样式，而第二个途径当某种改变发生（如Hover）时候才能获取样式，这样就会产生过渡动画。

![image-20211127175643917](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211127175643917.png)

### transition：过渡效果

### CSS中无继承的属性：

display:规定元素应该生成的框的类型

文本属性： vertical-align:垂直文本对齐、text-decoration规定添加到文本的装饰、text-shadow文本阴影效果、white-space空白符的处理 unicode-bidi：设置文本方向

盒子模型的属性：width height margin border padding

背景属性： background 

定位属性： float、 clear position  top、right、bottom、left   min-width... max-height... z-index clip



### 有继承性的属性：

**字体系列属性**

-  font-family：字体系列 
-  font-weight：字体的粗细 
-  font-size：字体的大小 
-  font-style：字体的风格 

**文本系列属性**

-  text-indent：文本缩进 
-  text-align：文本水平对齐 
-  line-height：行高 
-  word-spacing：单词之间的间距 
-  letter-spacing：中文或者字母之间的间距 
-  text-transform：控制文本大小写（就是 uppercase、lowercase、capitalize 这三个） 
-  color：文本颜色





### align-item 和align-content的区别： align 排列

align-items: center 对于每一个单行容器居中，而不是整个

align-content： center **只适用于多行的容器**，并且当竖直方向轴有多余的空间，将flex线在伸缩容器内对齐。



![image-20211128143956487](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211128143956487.png)

block(块元素)：会独占一行，多个元素会另起一行，可以设置wid hei margin padding 等属性

inline（行内元素）：元素并不会独占一行，设置width、height属性无效。但可以设置水瓶方向的margin和padding属性，不能设置垂直方向的padding和margin

inline-block: 将对象设置为inline对象，但对象的内容作为block对象呈现，之后的内联对象会被排列在同一行内。

### 

### 行内元素和块元素：

1.行内：设置宽高无效、可设置水平margin padding 但垂直方向无效 、 不会自动换行

2.块：可设置宽高、可设置margin padding、可以自动换行、多个块状，默认排列从上到下

![image-20220218134138431](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220218134138431.png)



### 隐藏元素方法：

display:none   visibility:hidden  opacity:0  position:absolute   z-index:负值  clip-path（元素裁剪的方法来实现隐藏，这种方式元素仍在页面中占据位置但是不会影响绑定的监听事件） transofrm:scale(0,0)

（将元素缩放为0，来实现隐藏，这种也是仍占位置不会影响事件）



### link和@import区别：

两者都是外部引用CSS的方式，他们的区别如下：

link是XHTML标签，除了加载css之前还可以定义一些RSS等其他事物，@import属于CSS范畴，只能加载css.

**link引用CSS时，在页面载入时候同时加载，而@import需要页面完全载入以后加载**

link是XHTML标签，无兼容问题；@import是在CSS2.1提出的，低版本浏览器不支持

**link支持使用Js去操作DOM改变样式 @import不支持**



### 伪类和伪元素：

伪类就是当一个处于特定状态的元素的选择器，比如某一个class的第一个元素，某个被hover的元素等等，我们可以理解成特定的CSS类，但与普通的类不一样，他只有处于DOM树无法描述的状态下才能为元素添加样式，所以称其为伪类

伪元素：类似于增添一个新的DOM节点到DOM树中，而不是改变元素的状态，并且并不是真的增加了一个结点。

伪类是操作文档中已经有的元素，而伪元素是创建了一个文档外的元素 

伪类：  比如 :hover   伪元素比如： ‘：：before‘



### 盒子模型：

1.盒模型都由四部分组成 margin border padding和content

2.标准盒模型和IE盒模型（怪异盒）区别在于设置width和height时，对应的范围不同：

​	·标准盒模型width和height 只包括了content

<img src="http://img.smyhvae.com/2015-10-03-css-27.jpg" alt="img" style="zoom:67%;" />

​    ·怪异盒子模型width和height 不仅仅有content 还有border和padding

<img src="http://img.smyhvae.com/2015-10-03-css-30.jpg" alt="img" style="zoom:67%;" />

3.可以通过修改**box-sizing：content-box 或者 border-box来改变元素的盒子模型**，默认值为标准盒

### 设置行内元素宽高：在标准盒子的情况下给行内元素设置高度是不生效的，但是在IE盒子模型下会生效

### 设置百分比高度：一个元素高度就是由其包括内容而决定的，如果父元素没有设置百分比高度，子元素设置了一个百分比高度是无效的。

### 

### CSS预处理器

预处理器为CSS增加了编程特性，无需考虑浏览器的兼容问题，可以在CSS中使用变量，可以让CSS更加的简介，增加适应性以及可读性，可维护性



### 双边距重叠问题（外边距重叠）：

多个相邻（兄弟或者父子元素）普通流的块元素垂直方向的margin会重叠

折叠结果：两个相邻的外边距都是正数时，折叠结果是它们两者之间较大的值。 

​				  两个相邻的外边距都是负数时，折叠结果是两者绝对值的较大值。

 				 两个外边距一正一负时，折叠结果是两者的相加的和。

### 

### 文本溢出隐藏：

单行：  overflow :hidden;

 			text-overflow:ellipsis

​			 white-space:nowrap

多行： overflow:hidden;

​			text-overflow:ellipsis;

 			display: -webkit-box; 设置为弹性伸缩盒子模型

​			-webkit-box-orient:vertical 设置伸缩盒子的子元素排列方式：从上到下垂直排列

​			-webkit-line-clamp:3  显示行数



### z-index失效的情况:

通常z-index的使用是在有两个重叠标签，在一定情况下控制其中一者在另一者上方或者下方出现。z-index值越大就越是在上层，**z-index元素的position属性需要是relative absolute 或者是fixed**

z-index在以下情况会失效：

1.父元素position为relative时候，子元素的z-index会失效。

2.元素没有设置Position属性为非static属性

3.元素在设置了z-index同时还设置了float



### 定位：

sticky

**Internet Explorer、Edge 15 以及更早的版本不支持粘性定位。 Safari 需要 -webkit- 前缀（请参见下面的实例）。您还必须至少指定 **top**、**right**、**bottom** 或 **left** 之一，以便粘性定位起作用。
