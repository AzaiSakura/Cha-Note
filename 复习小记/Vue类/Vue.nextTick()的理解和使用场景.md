### Vue.nextTick()的理解和使用场景

### 应用场景：

需要在视图更新之后，基于新的视图进行操作

### 文档说明：

在下次DOM更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的DOM

### nextTick原理：

1、异步说明

Vue实现响应式并不是数据发生变化后DOM立即变化，而是按一定的策略进行DOM更新

2、事件循环说明

简单来说，Vue在修改数据后视图是不会立即更新的，而是等同一时间循环中所有数据变化完成后，在同一事件循环中的所有数据变化完成之后，在统一进行视图更新。

created、mounted

在created和mounted阶段，如果需要操作渲染后的视图，也要使用nextTick方法。

注意Mounted不会承诺所有的子组件也都一起被挂载，如果你希望等到整个视图渲染完毕，可以用vm.$nextTick替换掉Mounted

```js
mounted(){
	this.$nextTick(function (){
		//这里面的代码会在数据更新后才渲染
	})
}
```

以下是使用nextTick的三种情况

1.点击按钮显示原本v-show =false隐藏起来的输入栏，并获取焦点。

```js
showsou(){
  this.showit = true //修改 v-show
  document.getElementById("keywords").focus()  //在第一个 tick 里，获取不到输入框，自然也获取不到焦点
}
```

```js
showsou(){
    this.showit = true;
    this.$nextTick(function (){
        document.getElementById("keywords").focus();
    })
}
```

2、点击获取元素宽度

3、使用swiper插件通过ajax请求图片后的滑动问题

