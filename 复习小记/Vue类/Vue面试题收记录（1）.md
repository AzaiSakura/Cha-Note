## Vue面试题收记录（1）

#### Vue中的路由拦截是怎么做的？

在全局前置函数中通过to和next参数，参照当前用户权限对路由重定向 

### 1.Vue3与Vue2相比较，有哪些变化

组合式API、setup（）、生命周期destoyed被unmounted替换 、teleport、双向绑定的优化、reactive和ref  、diff优化、Vue对象的重任卸给了app对象，并且减轻了对象压力删除了不常用的一些api

#### Vue3不兼容IE8的原因：Vue3中大量使用了ES6的语法，比如响应式处理改用Proxy，通过Reflect读写代理对象属性，通过Set收集视图依赖，Map建立代理对象属性与Set依赖的映射关系

![image-20211206175606202](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211206175606202.png)



### 2.在Vue中修改数据后，视图没有更新，是什么原因

#### 数据更新视图没更新，这说明是vue并未检测到属性的改变，如果是对象那么可能是直接新增或者是删除了属性，或者是数组，或者数据不是响应式的，vue不能检测数组直接通过索引修改数组项

那是vue的域更新是异步的，因为即当setter操作发生后，并不会立马更新，指令的更新操作会有延迟，当指令更新真正执行的时候，此时修改的属性属性已经设置，所以指令更新模板时得到的是新值。

### 3.$nextTick在什么场景使用，实现原理

Vue中的DOM是异步更新的，当数据已经获取到最新的时候想要获取最新的DOM就需要用nextTick

this.$nextTick(cb) 当数据更新时,dom渲染后会自动执行callback函数（为了取得最新的dom）

需要在视图更新之后，基于新的视图进行操作。

this.$nextTick()方法主要是用在数据改变，dom改变应用场景中。vue中数据和dom渲染由于是异步的，所以，**要让dom结构随数据改变这样的操作都应该放进this.$nextTick()的回调函数中。created()中使用的方法时，dom还没有渲染，如果此时在该钩子函数中进行dom赋值数据（或者其它dom操作）时无异于徒劳，所以，此时this.$nextTick()就会被大量使用，而与created()对应的是mounted()的钩子函数则是在dom完全渲染后才开始渲染数据，所以在mounted()中操作dom基本不会存在渲染问题。**   //从生命周期为起始点来思考这道题

在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

### 实现原理：

在Vue2.4版本之前nextTick几乎是基于microTask实现的，但是由于microTask的执行优先级太高了，在某些情况下，他甚至比事件冒泡还要快，就会导致一些诡异的问题，但是如果全部换成了macroTask，对一些重绘和动画的场景又会有性能影响。所以，最终nextTick的采用策略是默认走microTask,对于一些DOM的交互事件，比如v-on绑定的事件回调，会强制走macroTask。

### 异步更新视图的好处：当数据同步更新时候DOM会出现频繁渲染视图导致造成页面卡顿，极端的消耗资源，所以异步更新打打提升了性能，并且数据更新很高效，体验并没有降低

### 4.虚拟dom和真实dom区别，diff算法的理解

### 5.通信传值的方法有哪些

props emit自定义事件传值 provide inject   EventBus（兄弟组件互传）

### 6.插槽和作用域插槽的的理解

父组件向子组件传递内容

父组件在引用子组件的时候中间插入值

```
//parent
<template>
	<children>
		<template v-slot:name1>
		插槽中的内容
		</template>
	</chilren>
</template>
```

```
//child
<template>
	<slot name="name1"></slot>//这里就是父组件插入的内容
</template>
```

### 作用域组件能够让子组件向父组件传递值



### 7.Vuex中出现重名的方法如何解决

Vuex中有模块的感念，命名空间

```js
const store = new Vuex.Store({
  modules: {
    account: {
      namespaced: true,

      // 模块内容（module assets）
      state: () => ({ ... }), // 模块内的状态已经是嵌套的了，使用 `namespaced` 属性不会对其产生影响
      getters: {
        isAdmin () { ... } // -> getters['account/isAdmin']
      },
      actions: {
        login () { ... } // -> dispatch('account/login')
      },
      mutations: {
        login () { ... } // -> commit('account/login')
      },

      // 嵌套模块
      modules: {
        // 继承父模块的命名空间
        myPage: {
          state: () => ({ ... }),
          getters: {
            profile () { ... } // -> getters['account/profile']
          }
        },

        // 进一步嵌套命名空间
        posts: {
          namespaced: true,

          state: () => ({ ... }),
          getters: {
            popular () { ... } // -> getters['account/posts/popular']
          }
        }
      }
    }
  }
})
```



### 8.methods，watch，computed

#### methods:用来定义函数，需要我们手动调用

#### 计算属性computed:

1、**支持缓存，**只有依赖数据发生变化时候才会重新进行计算

2、**不支持异步**，当computed内有异步操作时无效，无法监听数据的变化

3、computed属性值会默认走缓存，计算属性是基于他们的响应式依赖进行缓存的，也就是基于data中声明过或者父组件传递的props中的数据通过计算得到的值

4、如果一个属性是由其他属性计算而来，这个属性依赖其他属性，是一个多对一或者一对多，一般使用computed

5、如果computed属性属性值是个函数，那么默认会走get方法，函数的返回值就是属性的属性值，在computed中，属性都有一个Get和set方法，当数据发生变化的时候就调用set方法

#### 侦听属性watch：

1·、不支持缓存，数据变化**直接会触发响应的操作**

2、watch支持异步

3、监听的函数接受两个参数，第一个是最新值，第二个是旧值

4、当一个属性发生变化时，需要执行对应的操作；一对多；

5、监听属性必须是data中声明过的或者父组件中传递过来的props中的数据嘛，当数据变化时，触发其他操作，函数有两个参数，

​		immediate：组件加载立即触发回调函数执行

​		deep：深度监听，为了发现内向内部值的变化，复杂类型的数据时使用，例如数组中的对象的内容改变，注意监听数组的变动不需要这么做，注意：deep无法监听到数组的变动和对象的增加，参考vue数组变异，只由以响应式的方式出发才会被监听到。

### 9.Vue中key的作用，什么时候用key

key` 的特殊 attribute 主要用在 Vue 的虚拟 DOM 算法，在新旧 nodes 对比时辨识 VNodes。如果不使用 key，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试就地修改/复用相同类型元素的算法。而使用 key 时，它会基于 key 的变化重新排列元素顺序，并且会移除 key 不存在的元素。

有相同父元素的子元素必须有**独特的 key**。重复的 key 会造成渲染错误。

### 10.$set、$once的理解

$once 表示监听一个dom，并且这个事件他只会触发一次

### 11在生命周期中，updated的用法，什么场景用到他，如果在updated中写入值的改变，视图会修改吗

注意，`updated` **不会**保证所有的子组件也都被重新渲染完毕。如果你希望等到整个视图都渲染完毕，可以在 `updated` 里使用 [vm.$nextTick](https://cn.vuejs.org/v2/api/#vm-nextTick)：

```js
updated: function () {
  this.$nextTick(function () {
    //  仅在整个视图都被重新渲染之后才会运行的代码     
  })
}
//在服务器端渲染期间是没有Updated这个生命周期的
```



然而在大多数情况下，你应该避免在此期间更改状态。如果要相应状态改变，通常最好使用[计算属性](https://cn.vuejs.org/v2/api/#computed)或 [watcher](https://cn.vuejs.org/v2/api/#watch) 取而代之。一般在[vue](https://so.csdn.net/so/search?from=pc_blog_highlight&q=vue)2会把请求放在created()和mounted()生命周期引用,

因为在updated修改数据是会触发beforeupdated的 因此如果在这段时间内发送事件，就会一直发

this.$forceUpdate()会更新视图和数据

### 12.在父子组件中，加载渲染、子组件更新时定位生命周期执行顺序

```
父beforeCreate->父created->父beforeMount->子beforeCreate->子created->子beforeMount->子mounted->父mounted
```

```
父beforeUpdate->子beforeUpdate->子updated->父updated
```

​		父beforeUpdate->父updated

​		父beforeDestroy->子beforeDestroy->子destroyed->父destroyed

### 13.filters、computed、watch在哪一个生命周期执行

beforecreate 和 created之间  因为此时才初始化了数据监测，和数据代理

注意，**不应该使用箭头函数来定义 watcher 函数** (例如 `searchQuery: newValue => this.updateAutocomplete(newValue)`)。理由是箭头函数绑定了父级作用域的上下文，所以 `this` 将不会按照期望指向 Vue 实例，`this.updateAutocomplete` 将是 undefined。

### 14.keep-alive的理解，使用keep-alive缓存页面时，例如在页面跳转查看商品列表，商品是实时更新的，返回再进入列表时，会渲染上一次缓存的页面数据，如何解决。

通过上面已经知道了被keep-alive包裹的页面会被缓存。同时知道生命周期执行顺序：

使用keep-alive的：beforeRouteEnter --> created --> mounted --> activated --> deactivated
使用keep-alive，再次进入了缓存页面：beforeRouteEnter -->activated --> deactivated。

所以只需要使用keep-alive结合router缓存需要缓存的页面，然后把需要刷新的数据放在页面的activated 钩子函数中。

```
 activated(){
    //需要刷新的页面方法
     this.getSubsideList();
  },
```



### 15.Vuerouter的理解，路由导航守卫有哪些，有导航切换时守卫的执行顺序

router.beforeEach（to,from,function）

```js
router.afterEach((to, from) => {
  // ...
})
```

还有可以在路由配置上直接定义beforeEnter 路由独享守卫

### 17.Vue如何获取并操作Dom元素

直接给相应的元素加id,然后再document.getElementById("id");获取.

使用ref,给相应的元素加ref=“name” 然后再this.$refs.name获取到该元素

### 18.vue的extends的理解和使用

可以看到，`extend` 创建的是 Vue 构造器，而不是我们平时常写的组件实例，所以不可以通过 `new Vue({ components: testExtend })` 来直接使用，需要通过 `new Profile().$mount('#mount-point')` 来挂载到指定的元素上。

在 vue 项目中，我们有了初始化的根实例后，所有页面基本上都是通过 router 来管理，组件也是通过 `import` 来进行局部注册，所以组件的创建我们不需要去关注，相比 `extend` 要更省心一点点。但是这样做会有几个缺点：

1. 组件模板都是事先定义好的，如果我要从接口动态渲染组件怎么办？
2. 所有内容都是在 `#app` 下渲染，注册组件都是在当前位置渲染。如果我要实现一个类似于 `window.alert()` 提示组件要求像调用 JS 函数一样调用它，该怎么办？

这时候，`Vue.extend + vm.$mount` 组合就派上用场了。

### 19.双向绑定的实现

![image-20211206175902485](C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20211206175902485.png)

 Vue通过采用数据劫持结合发布者-订阅者模式的方式，通过Object.defineProperty()来**劫持各个属性**的getter和setter，在数据变动时候发布消息个订阅者，触发监听回调，以此实现数据变化，视图跟着变化，视图变化，数据也随之发生变化。

MVVM是作为数据绑定的入口，整合了Observer、Complie、Watcher三者，通过Observer监听自己的model变化，通过Complie来解析指令，最终利用Wathcer搭起Observer和Complie之间的通信桥梁，达到数据变化->视图更新；视图交互变化->数据model变更的双向绑定效果。

**Vue中使用到了碎片化文档（DocumentFragement）这个概念，他可以把他任务的一个DOM节点当作收容器，当我创造了十个节点，当每个节点都插入到文档中都会引发一次浏览器回流，也就是说，浏览器要回流十次十分浪费资源，而使用了文档化碎片，就把10个节点放到一个容器中，最后我们再把容器直接插入到文档就可以了。浏览器只回流了一次。**

Vue框架中的依赖收集的任务是交给Watcher中的Dep来收集依赖的，其中通过将绑定的订阅者加入到数组中，一旦变更就通过触发notify（）方法，来更新所有的订阅者信息。

### 20.Vue data为什么是函数？

**vue中data必须是函数是为了保证组件的独立性和可复用性**，data是一个函数，组件实例化的时候这个函数将会被调用，返回一个对象，计算机会给这个对象分配一个内存地址，你实例化几次，就分配几个内存地址，他们的地址都不一样，所以每个组件中的数据不会相互干扰，改变其中一个组件的状态，其它组件不变。

### 21.Vuex中mutation中的函数只能是同步的，如果是异步的需要放在action里面并且action中的方法通过$store.dispath()来触发

### 22.setup（）为什么不能是async函数？

**原因是setup函数是一个同步函数，而请求接口时异步，异步函数.then里的操作是在同步操作后，因此需要把echarts初始化函数写在then里**

**可以通过<suspense>标签来包裹这个异步组件的时候就可以把setup()函数设置为async函数**



### Vue中scoped的实现原理以及scoped的穿透用法

scoped实在vue文件中的中的style标签上特有的一个特殊属性，当一个style标签拥有scoped属性之后，它的CSS样式只能用于当前Vue组件，可以使用组件的样式不相互污染。如果一个项目的所有style标签都加上了scoped属性，相当于实现了**样式的模块化。**

### scoped实现原理主要是通过PostCss实现的。

转义前的代码

```vue
<style scoped>

    .example{

        color:red;

    }

</style>

<template>

    <div>scoped测试案例</div>

</template>
```

转义后的代码：

```vue
.example[data-v-5558831a] {

  color: red;

}

<template>

    <div class="example" data-v-5558831a>scoped测试案例</div>

</template>
```

### 原理：vue的scoped是通过PostCss给一个组件中所有的DOM元素都添加了一个独一无二的动态属性，给css选择器额外添加了一个对应的属性选择器，来选择组件中的DOM，这样就能够让样式只作用要于含有该属性的DOM元素（组件内部DOM）实现了样式的组件化。

## Scoped穿透：

### 当使用第三方组件库的时候可以涉及到一个scoped穿透问题

```html
//以scss为例子
<style scoped lang='scss'>
$color:blue;
    .main{
        background:$color;
    }
</style>
```



scoped看起来很好用，当在Vue项目中，我们需要引入第三方组件库时，需要在局部组件中修改第三方组件库的样式，而又去除scoped顺序造成的组件之间样式覆盖，这时候我们可以通过特殊方式的穿透scoped

```css
外层 >>> 第三方组件

     样式
     
.wrapper >>> .swiper-pagination-bullet-active

 background: #fff
```

sass和less的样式穿透使用/deep/

```css
外层 /deep/ 第三方组件 {

    样式

}
.wrapper /deep/ .swiper-pagination-bullet-active{

  background: #fff;

}
```

### 其他修改第三方组件库样式的方法：

在Vue组件中不适用scopoed属性在vue组件中使用两个style标签，一个加scoped 一个不加，把需要覆盖的css样式写在不加scoped的style标签里面建立一个reset .css文件，里面写覆盖的css样式在入口文件main.js中引入



### Vue中数组更新的检测：

Vue2中是无法对数组进行响应式设定的，原因是Object.defineProperty只能劫持对象属性，所以Vue只重写了一套观察数组的变异方法，通过他们也将会触发视图的更新。但对数组新增一个属性（index）不会监测到数据变化，因为无法监测到新增数组的下标（index），删除一个属性（index）也是。

```js
push()
pop()
shift()
unshift()
splice()
sort()
reverse()
```

