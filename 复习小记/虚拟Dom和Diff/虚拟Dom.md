## 虚拟Dom

虚拟Dom是用Javascript对象描述Dom层次，真实DOM中一切属性都在虚拟DOM种有对应的属性。

![image-20220103112509387](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220103112509387.png)

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220103112538995.png" alt="image-20220103112538995" style="zoom:50%;" />

**diff是发生在虚拟DOM上的**

新的虚拟DOM和老虚拟DOM进行diff(精细化比较)，算出应该如何最小量更新，最后反应到真实的DOM上。

### 虚拟DOM是如何产生的？

虚拟DOM是通过h函数（渲染函数）产生的

h函数是创建**虚拟节点**（vnode）的

```js
const vnode1 = h('a',{props:{herf:'http://www.baidu.com'}},'百度')
就可以获得这样一个节点
<a herf="http://www.baidu.com">百度</a>
这样并不能创建一个真正的dom结点上dom树，但是可以通过console输出出来

```

**虚拟节点的属性：**

children：子元素节点

data：数值

elm：对应的真正的dom节点

key：唯一标识

sel：标签类型

text：文本内容

props：代表属性，properties，所以不仅仅可以有一个属性，可以有很多属性

```js
 //创建出patch函数
  const patch =init([classModule,propsModule,styleModule,eventListenersModule]);
  //创建虚拟节点
 const myVnode1 = h('a',{props:{href:'https://www.baidu.com'}},'百度')

 console.log(myVnode1);

 //让虚拟节点上树
 const container = document.getElementById('container');
 patch(container,myVnode1);
//这样就能让界面真实出现一个a标签
```

一个patch中，一个container只能挂在一个节点，但是h函数是可以内嵌的，

```
const vnode3=h('ul',[
     h('li','1'),
     h('li','2'),
     h('li','3'),
     h('li','4')
 ])
```

这种方式来内嵌就可以了一次性刷新多个节点

### diff算法原理？

diff算法会采用**最小量更新**的方式来对dom节点进行操作

![image-20220104111041104](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220104111041104.png)

这里我们可以发现加入新虚拟节点E，他并不是在所有的节点前面直接插入节点E的，而是通过让A变E ，B变A，C变B，D变C，然后在后面加入一个新的一个虚拟节点复制D。是这样一种方式。

**如果这里不加入key来设置唯一表示，那么就会采用上述的这样一个方式一共五步，但是我们加入了key之后，每个vnode具有了唯一性，这样他就不会采用上述方式，而是直接在所有节点前新加入vnode E 这一步，这就是为什么Vue中v-for一定一定要加入Key的原因，key就能够让vnode具有唯一性，这样能够让vdom有更好的性能。**

如果是要修改父节点类型，那么就会整替换，比如上面的ul换成ol，那么就会**整体进行一个替换**。就会让整体一个dom进行替换掉。

这里我们就引出一个新的概念就叫做**同层比较** 比如我们上述问题中就是，第一层父节点的比较出现了不同，所以他替换了整个dom树。这就是diff算法的同层比较。**只有是同一个虚拟节点才会精细化比较**。

当没有key关键字时，不会有精细化对比，直接暴力替换。并且所有的子节点都是需要递归创建的。





### 虚拟dom变成真正dom？

patch()函数可以将虚拟节点挂载到真正的dom树上面

![image-20220104173046276](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220104173046276.png)





在diff处理相同父节点但子节点不同时候

![image-20220110203550324](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220110203550324.png)

比如此时M N为新的节点的时候，就需要主要不是等所有处理节点之后，而是要在所以为处理的节点这里就是指的C之前



### 实际Diff的节点处理办法：

![image-20220113162602460](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220113162602460.png)

命中规则：

<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220113163217326.png" alt="image-20220113163217326" style="zoom:50%;" />



新前与旧前 指针相同时候则命中1，命中一种就不在判断命中了， 命中1之后 新前和旧前指针，往下移一位，如果1没命中，那么就会开始判断旧后和旧前节点，如果命中那么旧后节点和新后节点往上移动一位，如果2没命中，那么就会判断3号，新后指针和旧前指针，如果命中，那么会移动DOM，将旧前指针指到内容设置为undefined,然后将新后指针所指内容放到旧后节点后面，然后旧前指针往下移动一位，新后指针往上移动一位，如果3号没命中，那么就会进行4号判断，新前和旧后进行判断，如果命中4号判断，那么会将旧后指针所指内容设置为undefined然后将新前指针所指向内容放到旧前节点前面，然后新前节点往下移动一位，旧后节点往上移动一位。
（3、4命中规则总结：新前找到往旧前放，新后找到往旧后放。）
