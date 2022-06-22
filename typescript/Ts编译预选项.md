## ts的编译预选项

跟js不一样的是，ts需要首先让我们的ts文件 tsc转化成js才能编译，这样就很麻烦，于是我们出现了一些编译选项让我们的编译更加优雅更加得心应手。

但是我们每一次都需要进行tsc 手动编译，所以我们就可以设置

**tsc name.ts -w 让我们的编译模式 让name.ts文件可以自动编译**

但是这种方式只能监视一个ts文件，那么如果有很多很多文件，那么就得开大量终端

因此，我们可以设置多个监听和其他配置

我们需要建立tsconfig.json文件来对ts进行需求配置

目前就是空的也行

这样我们直接在终端开启 tsc -w 命令就能直接监听所有ts文件

### tsconfig.json文件是配置ts编译器的配置文件，ts编译器可以根据它的信息来实现定制化代码翻译，需要放在根目录才生效

### 配置项：

```tsx
{
    //用来指定哪些ts文件需要被编译
    "include": [
        "./src/xx/x"  //src目录下的某些文件被翻译 xx表示任意目录 x表示任意文件
    ],
        //用来指定哪些ts文件不需要被编译
     "exclude":[
         "./src/xx/x2"
        //默认值：["node_modules","bower_components","jspm_packages"]
     ]
    //定义被继承的配置文件
    "extends":[
        "./configs/base"
        //表示，当前配置文件中会自动包含config目录下base.json中所有配置信息
    ],
    "files":[
        //指定被编译的文件列表，只有编译文件较少的时候才用到，一般用Include
        "base.ts",
        "core.ts",
        ....
    ],  
        //编译器的配置选项
    "compilerOptions":{
        
    }
}
```

```json
{
    //配置编译器的配置选项
   "complierOptions":{
       //用来指定ts被编译的版本，默认版本是ES3  最新版本用 ESNext 表示
       "target":"ES6",
       //用来指定ts的模块化规范
       //option must be: 'none', 'commonjs', 'amd', 'system', 'umd', 'es6', 'es2015', 'es2020', 'es2022', 'esnext', 'node12', 'nodenext'.
       "module":"commonjs",
       //lib用来指定项目中药使用到的库,一般来说不需要设置这个配置项
       "lib":["dom",.....]
       ,
       //解析完毕后的文件所在目录
       "outDir":[
           "./dist",
       ],
       //将编译后的代码合并为一个文件,设置这个选项后，所有的编译并且在全局作用域中的代码都会合并到这个文件中
       "outFile":[
        "./dist/app.js"
       ],
       "allowJS":false//or true  表示是否对js文件进行编译，默认是false
       ,
       "checkJS":false// 表示是否检查js文件，默认是false
       ,
       //是否移除注释
       "removeComments":false //一  般来说会连注意都一起编译过去
       ,
       "noEmit":false //不生成编译后的文件
       ,
       "noEmitOnError": false //当出现错误时不生成编译后的文件
       ,
       "alwaysStrict":false//是否使用严格模式
       ,// "use strict"就能开启严格模式
       //不允许隐式any
       "noImplicitAny":false //一般来说在ts里面我们一直定义let b 这样一个变量他会默认变成any，但是any会导致类型检查消失一般不使用
       ，
       //不允许不明确类型的this
       "noImplicitThis":true,
       //严格的检查空值
       "strictNullCheck":true,
       ,
       //所有严格检查的总开关
       "strict":false,
       
   }
}
```

```tsx
let box1 = document.getElementById("box1");
//这个box1有可能没有这个dom元素，所以Box1有可能是空值
//如果设置了严格检查空值
box1.addEventListener('click',function (){});//就会报错
//所以我们可以这样写
box1?.addEventListener('click',function (){});
```

