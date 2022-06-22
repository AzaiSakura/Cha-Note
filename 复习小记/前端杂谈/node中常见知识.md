## Node中常见知识

### Node报错500

服务器端出现BUG崩了可能会出现500报错

还有当req.body.data 如果被JSON.parse()解析两次，就会报错500



1、数据导出

```js
//1.方式1
exports.num = num;
exports.sum = sum;
exports.Animal = Animal;
//2.方式2
// 通过module.exports 等于一个对象，来导出数据
// 对象可采用es6简化对象的写法
module.exports = {
    num,
    sum,
    Animal
};

```

2、数据导入

```js
// 注意1： 如果要使用某个模块里面的数据，则需要使用 require 关键字进行导入。
// 注意2：在导入用户自己开发的模块的时候，需要加上路径（1. 相对路径（多） 2. 绝对路径） 注意: ./ 必须写上
// 注意3：模块文件的扩展名(后缀名)可以写，也可以不写
// 注意4：导出的模块一般需要使用一个变量来接收，一般把接收的量定义为常量
// 注意5: 定义常量的名称和文件的名称保持一致（这个不是必须，大家都这么做）
const m1 = require("./modules/m1.js");

```



## Node中this指向问题

exports实际上是moudle.exports的引用

在nodejs里面的**this就是代表的当前模块，也就是exports的这个对象。**

```js
console.log(exports);   //{}

console.log(module.exports);  //{}

console.log(exports === module.exports);  //true  exports实际上是module.exports的引用

console.log('this', this); // this {}

console.log(this === exports);// true   // 在 nodejs 里面的 this 代表当前的这个模块，也就是 exports 对象  并且，交互模式下，没有exports这个对象

console.log(global === this );   //false    this不指向全局对象

```

## Nodejs常用内置模块

一般项目中分为三种，nodejs内置模块，自己书写的模块，第三方模块（使用一个专门的工具npm进行统一管理）

常用的内置模块如下：

```text
fs:文件操作
http：网络操作
path：路径操作
querystring：查询参数解析
url：url解析
```

```js
const fs = require("fs");
const http = require('http');
const path = require('path');
const querystring = require('querystring');
const url = require('url');
```



### Path内置模块

```js
const path = require("path");

console.log(__dirname); // 当前执行的文件 绝对路径，不包含文件名

console.log(__filename);  // 当前执行的文件 绝对路径，包含文件名

let extname = path.extname( __filename );   // 获取扩展名(后缀名)
console.log(extname);

let baseName = path.basename( __filename );  // 获取文件名(包含后缀名)
console.log(baseName);

let dirname = path.dirname(__filename);   //获取目录(路径)
console.log(dirname);

let parse = path.parse(__filename);  //获取路径字符串的对象
console.log(parse);

//路径的拼接操作 join
// join 默认相对路径的拼接 ，以当前操作系统路径分割符进行拼接
let fullPath1 = path.join('path.js');
// fullPath1 = path.join(__dirname,'path.js'); //带目录
// fullPath1 = path.join(__dirname,'a','path.js'); //带多级目录
console.log(fullPath1);

```

## Buffer数据类型

JS语言自身只有字符串类型，没有二进制数据类型，但是在处理文件时候（文件读写操作），必须使用到二进制数据。因此，在NodeJS中，定义了一个Buffer类，该类用来创建一个专门存放二进制数据的缓存区。说白了，Buffer类似于一个整数数组。

```js
let buf1 = Buffer.from([97, 98, 99]);  //根据一个数组创建 Buffer 对象
console.log(buf1);  //<Buffer 61 62 63> 以16进制存在buffer对象中
console.log(buf1.toString());

let buf2 = Buffer.from("nodejs");   //根据一个字符串创建 Buffer 对象
console.log(buf2);
console.log(buf2.toString());

let buf3 = Buffer.alloc(10);   // 创建了可以存放10个字符的buffer对象
buf3.write("abc");  //按照ASCII表的值，转16进制，存在buffer中
console.log(buf3);
console.log(buf3.toString());

// 总结： 以后看到 <Buffer .....  > 需要  toString()   才能看到里面的真实数据

```



### fs文件系统模块

Node.js的API内置有两个模块：PATH和FS，我们使用JS在Node.js环境中就可以操作文件了

#### 6.1同步读取文件信息

同步读取：读取的时候，要等文件读取完毕后，才会执行后面的代码

```js
const fs = require("fs");
const path = require("path");
let pathName = path.join(__dirname, 'hello.txt');

// const content = fs.readFileSync(pathName);   同步读取文件
// console.log(content);
// console.log(content.toString());

const content = fs.readFileSync(pathName, "utf-8");
console.log(content);

```

#### 6.2异步读取文件信息

不用等待文件读取完毕就会执行后面的代码

思考：如何读取到后面的数据

答：在读取文件的时候，传递一个回调函数callback，当读取完毕后，回调函数执行，在读取后面的数据

```js
const fs = require("fs");
const path = require("path");

let pathName = path.join(__dirname, "hello2.txt");
// console.log(file);
//参数1 要读取的文件
//参数2 设置读取到内容的编码，设置后读到的内容为字符串，如果不传则读到的数据为 buffer
//参数3 回调函数，读取完文件后执行的代码
fs.readFile(pathName, "utf-8",(error, data)=>{
    // console.log(error);
    // console.log(data);
   	if(error){
        console.log(error);
        return
    }
    console.log(data);
});

```



#### 6.3异步写入

```js
const fs = require("fs");
const path = require("path");

let pathName = path.join(__dirname, "hello.txt");


fs.writeFile(pathName, "hello_write111", "utf-8",(error)=>{
    console.log("error");
    console.log("写完啦");
});
console.log("end");

```

#### 6.4几个常见方法

```js
const fs = require("fs");
fs.renameSync(旧文件名, 新文件名);   //修改文件名
fs.readdirSync(__dirname);    //读取当前路径下的文件名列表

let str = "hello";
str.endsWith("he");   //true
str.startsWith("go");  //false
str.substring(2,4) //ll 左闭右开区间
str.substring(2) //llo  下标为2取到结束

```

