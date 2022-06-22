# 红宝书及ES6入门基础读后笔记



## 红宝书Part

1.

​	<script> 标签异步加载

​	defer属性、async属性 两个属性都是让scrpt标签异步加载的属性，其中

​	defer属性是在上面的scrpit标签先执行完毕，会**保证顺序**

​	async属性就是虽然是异步 但**不保证**多个异步加载的script标签加载的**顺序**

2.

### 	垃圾回收机制

​	标记清理法：当变量进入上下文时候比如在函数内部声明一个变量时，这个变量会被加上存在于上下文中的标记，逻辑上来讲，永远不应该释放他们的内存，因为只要上下文中的代码在运行，就可能用他们，当变量离开上下文时候就会被加上离开上下文标记。立即回收系统过程中，会标记内存中存储的所有变量然后，它会将所有上下文中的变量引用的变量的标记去掉，在此之后再被加上标记的就是的变量就是待删除的。

​	引用计数法：（不是很常用）每个值都记录他被引用次数，声明变量并赋予他一个引用值时，这个值引用数为1，如果同一个值又被赋予给另一个变量，那么引用值+1，当保存对该值的引用变量被其他值覆盖时引用值-1，当一个值的引用次数为0时，说明没办法访问这个值了，就可以安全的回收他的内存了。

3.

### 	内存泄露

​	在内存有限的设备上，或者在函数会被调用很多次的情况下，内存泄露可能是个大问题。

​	**声明全局变量是最常见也是最容易修复的内存泄露问题。**

​	除此之外，定时器也可能会悄悄导致内存泄露

```
let name = 'jake'
setTimeout(()=>{
	console.log(name);
},100);
//这种情况下，只要定时器一直运行回调函数中引用的name就会一直占用内存空间，垃圾回收程序知道这点，所以不会清除外部变量name
```

另外使用闭包也很容易在不知不觉间造成内存泄露

```
let outer = function (){
let name = 'jake'
return function(){
 return name ;
}
}
//只要返回函数存在就不能清理name,因为闭包一直在引用它，name只是字符串很小那还算小问题，但如果name是一个很大很大的对象，那可能就会对系统造成影响

```

这种方式的解决方法就是每次用完之后把持续引用的变量设置为null让系统回收

4.

### 	迭代方法

​	map() 对数组的每一项都运行传入的函数，返回数组由每次函数调用结构构成

​	forEach()对数组每一项都运行传入的函数，但是没有返回值。

​	filter（）对数组每一项都运行传入的函数，函数返回true的项会组成数组之后返回

​	every()对数组没一项都运行传入函数，如果每一项都返回true，则这个方法返回true

​	some()对数组每一项都运行传入函数，如果有任意一项为true,则这个方法都返回true

上面五种是非常常用的迭代方法，**他们都不改变调用他们的数组**

5.

```
	Object.defineProperty(对象，对象的属性，{

​		//	Object.definproperty的配置项

​		configurable: ture , //是否可以通过删除，来重定义

​		Enumerable：ture, //是否可枚举

​		Writable: true ,//是否可修改

​		Value:undefined，  //这就是前面提到的那个读取与写入值的位置

​		get:{},

​		set:{}
//其中get 和 set 中修改内容，就是vue2的响应式的原理

​	})
```

6.

箭头函数

箭头函数不能使用arguments super 和new.target也不能作为构造函数，此外，箭头函数也没有prototype属性。

**super指向当前对象的原型对象**

this关键字：

在一般函数中,this指代的是将函数当做方法使用的那个对象，

在箭头函数中，this指代的是在箭头函数声明时候，那个时候this的对象

除此之外，由于闭包的写法所致，this在闭包中的情况也有点特别

```
window.id = 'The Window'
let object ={
	id:'My object'，
	getid() {
		return function() {
			reutrn this.id
		}
	}
}
console.log(object.getid()());
	//会输出'The window'
	闭包中使用this会让代码变得复杂，如果内部函数没有使用箭头函数定义，则this对象会在运行时候绑定到执行函数的上下文，如果在全局函数中调用，this在非严格模式下等于window,在严格模式下，会等于undefined。如果作为某个对象方法调用，则this等于这个对象，匿名函数在这种情况下不会绑定到某个某个对象，这意味着this就指向了window，因此这里输出the window
```

```
//那么上述问题的解决方法？！ 需要使用另一个变量来存储我们的值
let object ={
	id:'My object'，
	getid() {
		let that =this;
		return function() {
			
			reutrn that.id
		}
	}
}
console.log(object.getid()()); 此时就会输出My object

```

7.

​	位运算

```
//按位 非操作符（~）
 let num =25 //二进制位000000000000011001
 let num2 = ~num //二进制位为 11111111111111100110
//按位与操作符（&）均为1为1 否则为0 
//按位或操作符（|）有1则为1 双0 才为0
// 按位异或(^)  :  只有一方为1 才为1 双方相同都为0
// 左移<<
// 右移>>
let old = 64 // 二进制1000000
let new = old>>5 //二进制10 相当于二进制的2


```

8.

​	判断NAN（not a number）的相等性

NaN(不是数值)表示本来要返回数值结果操作失败了

要判断NAN的相等性必须使用isNaN()

```
console.log(NaN === NaN) // false
console.log(isNaN(NaN)) //true
```

9.

​	for - in 可用于每局对象中非符号键属性(for-in不保证返回对象的属性顺序,因为不遍历符号键)

​	for- of 可用于遍历**可迭代对象的元素**（可迭代对象必须要有symbol.iterator）

10.

### 	JS的七种基本数据类型 ：非常6+1

​	ES6中有6种简单数据类型(undefined null number string symbol boolean)

​				还有一种复杂数据类型(Object)

​		可以使用typeof()方法来判断数据的基本类型

​		但如果需要判断某个变量是什么构造函数的时候需要使用instanceof

```
let p = new Promise(.....)
console.log(p instanceof Promise)//true
```

## ES10新增第八种JS数据类型BigInt

BigInt数据类型表示比Number数据类型支持的范围更大的整数值，在对大整数进行数学运算时候，以任意精度表示证书的能力尤为重要，使用BigInt，整数溢出将不再是问题。

## ES6入门基础Part