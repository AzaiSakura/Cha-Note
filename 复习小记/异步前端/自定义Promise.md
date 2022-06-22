## 自定义Promise

### 1.Promise整体结构

```js
function MyPromise(excutor) {
    //将当前promise对象保存起来
  const self = this;
  self.status = "pending";
  self.data = undefined;
  self.callbacks = []; //每个数据元素的结构{onResolved(){},onRejected(){}}

  function  resolve (value) {

      //如果当前状态不是pending就直接结束（说明前面已经执行过了）
      if(self.status !=='pending'){
          return
      }
    //改变状态为resolved
    self.status = "resolved";
    //保存数据
    self.data = value;
    //如果有待执行的Callback函数，就立即异步执行回调
    if (self.callbacks.length > 0) {
        
      setTimeout(() => {
        self.callbacks.forEach((items) => {
          items.onResolved(value);
        });
      });
    }
  }
  function reject (err) {

    if(self.status !=='pending'){
        return
    }
    //改变状态为rejected
    self.status = "rejected";
    //保存数据
    self.data = err;
    //如果有待执行的Callback函数，就立即异步执行回调
    if (self.callbacks.length > 0) {
      setTimeout(() => {
        self.callbacks.forEach((items) => {
          items.onRejected(err);
        });
      });
    }
  }

  //立即调用执行器
        try{
            excutor(resolve, reject);
        }catch (error){//如果执行器抛出异常，promise对象变成失败态
            reject(error)
        }
}

//指定成功失败的回调，返回一个新的promise对象 默认是undefined
MyPromise.prototype.then = function (onResolved, onRejected) {
    //假设当前状态还是pending状态，
    const self = this
    self.callbacks.push({
        onResolved,
        onRejected
    })
};
```

![image-20220117122319227](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220117122319227.png)

## promise.all()

```js
Promise.all = function (promises){
	let count = 0 记录成功个数
	const values = new Array(proises.length);
	return new Prmoise((resolve,reject)=>{
        promises.forEach((p,index)=>{
            Promise.resolve(p).then(
            	value =>{
                //p成功，将成功值存入values中
                    values[index] = value ;
                    count++ ; 
                //如果全部成功则return的Promise改成resolved态
                    if(valus.length===count){
                        resolve(values);
                    }
           	 },
                reason =>{
                    reject(reason)
                }
            )
        })
    })
}
```

## Promise.race()

```js
//返回执行最快的那个Promise的执行结果，也就是说，最先完成的那个之后就把返回的状态改成了成功态或者失败态
Promise.race = function (promises){
    return new Promise((resolve,reject)=>{
        promsies.forEach((p,index)=>{
            Promise.resolve(p).then(
            	value =>{
                  resolve(value)  
                },
                reason =>{
                    reject(reason)
                }
            )
        })
    })
}
```

