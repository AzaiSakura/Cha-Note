## CSS场景题之毛玻璃特效

毛玻璃特效看起来简单只需要首先给效果区域设置一个透明度，然后通过CSS的滤镜（filter）设置blur属性就行了，但是效果却不是我们想要的

```css
.content {
    background-color: rgba(0,0,0,0.3);
    -webkit-filter: blur(2px);
    -moz-filter: blur(2px);
    -ms-filter: blur(2px);
    -o-filter: blur(2px);
    filter: blur(2px);    
}
```



<img src="https://images2015.cnblogs.com/blog/613712/201607/613712-20160716195444592-1443748230.png" alt="img" style="zoom:67%;" />

上述过程中，我们可以发现：

·1、对元素直接使用模糊会将其内容全部模糊掉，为了保证文字不会被模糊掉，我们需要多一层单独的模糊效果。

2、模糊效果并不会作用在他背后的背景图片上，所以需要使用content区域有和背景相同的背景图并进行模糊。

### 根据问题对症下药

```css
//多一层层级的方法不通过添加元素，而通过伪元素
.content{
     z-index: 1;
}
.content:after{
    content: '';
    positon:'absolut';
    top:0;
    left:0;
    right:0;
    bottom:0;
    background-color: rgba(255,255,255,0.8);
    z-index:-1;
}

```

这里有两点需要注意，伪元素不能通过width:100%和height:100%,来继承宿主元素的尺寸，所以通过上述方式来继承content的尺寸，为了使伪元素位于content的下面，所以这里设置了一个z-index:-1;为了让content不使其隐藏到背景图的后面，这里给content: z-inde: 1;

<img src="https://images2015.cnblogs.com/blog/613712/201607/613712-20160716201413373-917682902.png" alt="img" style="zoom: 67%;" />

### 接下来针对第二个问题，让模糊效果作用到背景图片上

首先我们给content: :after {} 设置相同背景图

<img src="https://images2015.cnblogs.com/blog/613712/201607/613712-20160716201655373-1686135055.png" alt="img" style="zoom:67%;" />

如上图，即使我们设置了相同的background-position和background-size,中间部分的图和大背景还是没有完成拼接效果，

解决这个问题很简单，我们只需添加background-attachment:fixed 属性，之后为其进行模糊处理。

```css
.content {
    background-position: center top;
    background-size: cover;
}
.content::after {
    background-image: url(xxx.jpg);
    background-position: center top;
    background-size: cover;
    background-attachment: fixed;
    -webkit-filter: blur(20px);
    -moz-filter: blur(20px);
    -ms-filter: blur(20px);
    -o-filter: blur(20px);
    filter: blur(20px);
}
```

<img src="https://images2015.cnblogs.com/blog/613712/201607/613712-20160716202321717-699490181.png" alt="img" style="zoom:67%;" />

好了！我们离成功就差一点点了，这里是有没中不足的，元素边缘模糊效果减弱了，为了解决这个问题我们需要将伪元素扩大一些，同时为了效果不超出content的范围，给其设置overflow:hidden属性。

```css
.content {
　　overflow: hidden;
}
.content::after {
　　margin: -30px;
}
```

<img src="https://images2015.cnblogs.com/blog/613712/201607/613712-20160716202813014-209821418.png" alt="img" style="zoom:67%;" />

拿下