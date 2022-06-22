## 前端模块化，AMD和CMD规范区别

前端模块化将我们的网页应用分化成各个小的模块，这样就方便于我们以后都项目的维护以及管理

AMD是”Asynchronous Module Definition”的缩写，即”异步模块定义”。它采用异步方式加载模块，模块的加载不影响它后面语句的运行。

## UMD结合了CommonJS和AMD和CMD的特点

## 1、AMD推崇依赖前置(不可以动态加载模块)、CMD推崇就近依赖（可以动态加载模块）

```js
//AMD： mod.js
define(['dependency1', 'dependency2'], function(require, exports, module){
    //TODO

    module.exports = {};
});
```

```js
//CMD: mod.js
define(function(require, exports, module) {
    var $ = require('jquery.js')

    if(true) {
        let a = require('./a.js');
        //TODO
    }

    module.exports = {}
});
```

这种区别各有优劣，只是语法上的差距，而requireJS和SeaJS都对支持对方写法

## 2、执行时机不同：AMD是加载完立刻就执行，CMD是延迟执行（这是二者最大的区别）

```js
//CMD: mod.js
define(function(require, exports, module) {
    var $ = require('jquery.js')

    if(true) {
        let a = require('./a.js');
        //TODO
    } else{
        let b = require('./b.js');
        //TODO
    }

    module.exports = {}
});
```

在执行mod.js前，模板被解析为了字符串，然后通过正则表达式找出了模块中所有的依赖并一一加载，如例子中的jquery.js 、a.js、和b.js，但是加载完毕后依赖并不立即执行，而是要等到js执行到require语句的时候才被执行，如图例子中jquery.js和a.js会按照顺序执行，而b.js永远不执行

## 3、实现按需加载的方式不同

两种规范都可以实现按需加载，但是实现的API不同

```js
//AMD：
define(function(require, exports, module){
    document.getelementById('app').onclick = function() {
        require(['myModule'], function (my){
            my.printName();
        });
    }
    //TODO
    module.exports = {};
});

```

```js
//CMD：
define(function(require, exports, module){
    document.getelementById('app').onclick = function() {
        require.async('myModule', function (my){
            my.printName();
        });
    }
    //TODO
    module.exports = {};
});
```

## 

## Common.js和ES Module

Common.js定义的模块分为：模块引用(require)、模块输出(exports)、模块标识(module)

CommonJS Module 有1.0 、1.1、1.1.1三个版本：

​	Node.js、SproutCore实现了Modules 1.0

​	SeaJS、AvocadoDB、CouchDB等实现了Modules 1.1.1

​	SeaJS、FlyScript实现了Modules/Wrappings

这里的CommonJS指的是CommonJS Mdodules/1.0规范。

**CommonJS是一个更偏向于服务器端的规范。NodeJS采用了这个规范，CommonJS的一个模块就是一个脚本文件。require命令第一次加载该脚本时就会执行整个脚本，然后在内存中生成一个对象。**

- CommonJS 模块重复引入的模块并不会重复执行，再次获取模块直接获得暴露的 module.exports 对象
- 如果你要处处获取到模块内的最新值的话，也可以你每次更新数据的时候每次都要去更新 module.exports 上的值
- 如果你暴露的 module.exports 的属性是个对象，那就不存在这个问题了

所以如果你要处处获取到模块内的最新值的话，也可以你每次更新数据的时候每次都要去更新 module.exports 上的值，比如：

```js
// a.js
var b = require('./b');
console.log(b.foo);
setTimeout(() => {
  console.log(b.foo);
  console.log(require('./b').foo);
}, 1000);

// b.js
module.exports.foo = 1;   // 同 exports.foo = 1 
setTimeout(() => {
  module.exports.foo = 2;
}, 500);

// 执行：node a.js
// 执行结果：
// 1
// 2
// 2
```

由于 import 是静态执行，所以 import 具有提升效果即 import 命令在模块中的位置并不影响程序的输出。

```js
/ a.js
import { foo } from './b';
console.log('a.js');
export const bar = 1;
export const bar2 = () => {
  console.log('bar2');
}
export function bar3() {
  console.log('bar3');
}

// b.js
export let foo = 1;
import * as a from './a';
console.log(a);

// 执行结果:
// { bar: undefined, bar2: undefined, bar3: [Function: bar3] }
// a.js
```

从上面的例子可以很直观地看出，a 模块引用了 b 模块，b 模块也引用了 a 模块，export 声明的变量也是优于模块其它内容的执行的，但是具体对变量赋值需要等到执行到相应代码的时候。(当然函数声明和表达式声明不一样，这一点跟 JS 函数性质一样，这里就不过多解释)

### 模块不会重复执行

无论我们是ES6模块还是CommonJS模块，当我们重复引入某个相同模块时，都只会执行一次
