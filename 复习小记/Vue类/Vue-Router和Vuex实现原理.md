## Vue-Router和Vuex实现原理

路由这个概念先是由后端提出的，简单来说路由就是用来跟后端服务器进行交互的一种方式，通过不同路径，来请求不同资源，请求不同页面是路由的一种功能。



### 前端路由：

### 1.hash模式

随着ajax的流行，异步数据请求交互运行在不刷新浏览器的情况下进行，而异步交互体验更高级的版本就是SPA---单页应用。单页应用不仅仅是在页面交互是无刷新的，并且在页面跳转都是无刷新的，为了实现SPA于是就有了现在的前端路由。

**类似于服务器路由，前端路由实现也很简单，就是匹配不同url路径，对其进行解析，然后动态渲染出对应区域的html内容。**但是问题也非常显眼，就是每次url变化的时候，都会造成页面的刷新。那解决问题的思路便是在改变url的情况下，**保证页面不刷新**，在14年以前，大家通过hash来实现路由，url hash就类似于

```
http:///www.xxx.com/#/login
```

**这种# 后面的hash值的变化，并不会导致浏览器向服务器发出请求，浏览器不发出请求也就不会进行刷新页面**。**另外每次hash值的变化都可以触发hashchange事件，通过这个事件我们就可以知道hash值出现了什么变化，在对其进行一个监听来实现对应变化更新页面内容的操作。**

### 2.history模式：

14年以后，H5标准发布，多了两个API，**pushState和replaceState**，通过这两个API可以改变url并且不会发送请求。同时还有**popstate事件**，通过对这些事件的监听就能够实现前端路由了。



## Vuex实现原理

Vuex是专门为Vuejs应用程序设计的状态管理工具，它采用集中化式存储管理应用的所有组件的状态，并以响应的规则保证以一种可预测的方式发生变化。



<img src="C:\Users\11791\AppData\Roaming\Typora\typora-user-images\image-20220228220547521.png" alt="image-20220228220547521" style="zoom:67%;" />

state: 用来存储全局的基本数据

getters: 就是store中的计算属性

mutations:提交更加数据更改state状态（mutations是同步函数）

Actions:actions就像一个装饰器，提交mutation,而不是直接改变状态（actions可以包含任何异步操作）

Module：store分割的小模块，就相当于一个小模块拥有自己的state getters actions mutaitons

### 辅助函数：

Vuex提供了...mapState MapGetters  Mapactions mapMutations 等辅助函数给开发在vm中处理处理store.

## Vuex原理分析：

### 1.vuex的store是如何挂载注入到组件中的？

1.我们可以知道会有Vue.use()的方法来让vuex以插件的形式装载进我们的项目之中去

```js
//2.利用插件机制，当我们用Vue.use(vuex)时候，会调用vuex的install方法
export function install (_Vue) {
  if (Vue && _Vue === Vue) {
    if (process.env.NODE_ENV !== 'production') {
      console.error(
        '[vuex] already installed. Vue.use(Vuex) should be called only once.'
      )
    }
    return
  }
  Vue = _Vue
  applyMixin(Vue)
}
```

3.applyMixin方法使用vue混入机制，vue的生命周期beforeCreate钩子函数前混入vuexInit方法

```js
Vue.mixin({ beforeCreate: vuexInit });

function vuexInit () {
    const options = this.$options
    // store injection
    if (options.store) {
      this.$store = typeof options.store === 'function'
        ? options.store()
        : options.store
    } else if (options.parent && options.parent.$store) {
      this.$store = options.parent.$store
    }
}
```

### 问题2：vuex的state和getters是如何映射到各个组件中实现响应式更新状态的呢？

```js
function resetStoreVM (store, state, hot) {
  const oldVm = store._vm

  // 设置 getters 属性
  store.getters = {}
  const wrappedGetters = store._wrappedGetters
  const computed = {}
  // 遍历 wrappedGetters 属性
  forEachValue(wrappedGetters, (fn, key) => {
    // 给 computed 对象添加属性
    computed[key] = partial(fn, store)
    // 重写 get 方法
    // store.getters.xx 其实是访问了store._vm[xx]，其中添加 computed 属性
    Object.defineProperty(store.getters, key, {
      get: () => store._vm[key],
      enumerable: true // for local getters
    })
  })

  const silent = Vue.config.silent
  Vue.config.silent = true
  // 创建Vue实例来保存state，同时让state变成响应式
  // store._vm._data.$$state = store.state
  store._vm = new Vue({
    data: {
      $$state: state
    },
    computed
  })
  Vue.config.silent = silent

  // 只能通过commit方式更改状态
  if (store.strict) {
    enableStrictMode(store)
  }
}
```

我们可以发现vuex的**state状态是响应式的，是借助vue的data是响应式的，将state数据存入vue实例组件中的data中**，Vuex的**getters则是借助vue的计算属性computed实现数据实时监听**。

