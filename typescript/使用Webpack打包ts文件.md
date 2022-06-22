## 使用Webpack打包ts代码

在webpack.config.js中设置

```js
const path =require('path');

module.exports ={
    entry:'./src/index.ts',
    //打包文件所在目标
    output:{
        path:path.resolve(__dirname,'dist'),
        filename:"bundle.js"
    },
    //指定2webpack打包时候使用模块
    module:{
        //指定我们要加载的规则
        reules:[
            {
                //test指定的是规则生效的文件
                test:/\.ts$/,
                //要使用的loader
                use:'ts-loader',
                // 要排除的文件
                exclude:/node-modules/

            }
        ]
    }
};
```

