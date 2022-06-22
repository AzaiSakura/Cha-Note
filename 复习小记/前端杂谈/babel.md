## Babel介绍

Babel是一个js的编译器，简单来说就是把ES6转化成浏览器能兼容的ES5。他的职责包括：

1.语法转换

2、通过Polyfill方式在目标环境中添加缺失的特性（通过第三方引入Polyfill模块）

3.源码转换

.....

#### Babel可以删除类型注释，但是注意，Babel并不会做类型检测，所以我们仍然需要TS或者Flow来进行类型检测工作。

### Babel-polyfill作用：

Babel包含一个可自定义的regenerator runtime和core-js的polyfill

他会仿效一个完整的ES6+的环境，并且意图运行于一个应用中而不是一个库中。

preset与plugin的关系：

preset已经包含了一组用来转化ES6+的语法插件，如果只使用少数新特性而非绝大部分新特性，可以不适用preset而是用对应的转化插件。

**babel默认只转化语法而不转换新的API，**如果使用新的API还**需要使用对应的转化插件或者Polyfill**

默认情况下babel可以把箭头函数 class等语法转化为ES5的兼容形态，但是不能转化Map、Set、Promise等新的全局对象，这个时候就需要使用Polyfill去模拟这些新特性。

**Babel默认质转化新的js语法，而不转化API，如Iterator、Generator、Set、Map、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法都不会转码。**



