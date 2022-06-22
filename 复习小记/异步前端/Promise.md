## Promise

### promise.all([iterable])

promise.all(iterable)方法返回一个**新的**Promise实例对象，这**个实例在所有iterable参数内所有的promise都完成（resloved）时或参数中不包含promise时候回调完成，如果参数中promise有一个失败(rejected)此实例就无法回调，失败原因就是第一个失败promise的回调结果**。

![image-20220115111914369](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220115111914369.png)

像这种形式，values就返回所有成功结果放在一个数组中

### promise.race([iterable])

race的意思就是赛跑，意思说.race可迭代的里面**哪个结果获得的快就返回哪个结果**



### Promise的三种状态：

初始态：Pending 未确定态

Pending可以变为resolved态 成功态

也可以变成rejected态 失败态

只有Pending变化为其他两态之一种，而且 一个promise对象只能改变一次

另外 throw error 也可以让promise变成失败态

```js
new Promise((resolve,reject)=>{
setTimeout(()=>{
	resolve('1');
	reject('2');
	})
}).then(success=>{}).catch(err =>{})
//这种写法是错误的，因为promise状态只会改变一次，所以当resolve（）后，状态变为resolved态，就不会再执行下面reject('2')内容了
```



一般成功的值为data 失败为err（习惯上）

![image-20220113203821117](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220113203821117.png)



### Promise函数对象除了all和race，还有resolve（）和reject()两个语法糖

![image-20220113210624262](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220113210624262.png)



### promise.then(callback1,callback2)    其中 .then()是同步的，而其中的回调函数是异步的。then方法是指定回调函数，而不是回调函数，他里面的callback才是回调

```js
new Promise((resolve,reject)=>{
	resolve(1)
	//reject(1)
}).then(
	value =>{
		console.log('resolved1',value);
        //return ....
	},
	res =>{
		console.log('reject1',res);
	}
).then(
	value =>{
		console.log('resolved2',value);
	},
	res =>{
		console.log('reject2',res);
	}
)
//此时输出resolved1 1  和resolved2 undf  原因是第一个then里面没有返回值，没有返回值默认为undefined
```

通过.then可以链式调用多个同步/或者异步的回调函数，但是异步的回调必须包裹在promise里面（不promise怎么异步。。）

![image-20220115120639174](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220115120639174.png)

