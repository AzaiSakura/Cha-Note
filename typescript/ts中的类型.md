## TS中的类型

### 1、number 数字

### 2、string 任意字符串

### 3、boolean 布尔值

### 4、字面量 其本身 限制变量就是该字面量的值  let a :"male" |  "female"  比如这样就只能设置为male 或者female者两个字面量

### 5、any 任意类型   相当于关闭了TS的类型检测（所以开发中不建议使用any）

any类型的变量，无论自己是什么值，他可以赋给任意变量并且不会报错（所以不怎么推荐用）

### 6. unknown  类型安全的any

unknown也是可以表示任何类型，但是与any不同的是他不能赋予给其他变量，不像any那样，而是会报错

实际上就是一个类型安全的any，不能直接复制给其他变量，但是可以先进行类型判断，如果是和要赋予的变量类型相同那么可以赋予

```js
//unknown复制方式
let s:string ;
s='haha'
1、 let e:unknown;
	e='hello'
if(typeof e === 'string'){
    s = e ;
}
2、类型断言
s = e as string; 
或者 s = <string>e ;
是一样的
//类型断言可以用来告诉解析器变量的实际类型

```



### 7. void  空值(undefined) 没有值或者undefined

### 8.never 没有值 不能是任何值

### 9.object   {name:'achacha'}  任意js对象

#### 	对象的定义结构

let a:object; 一般不这样使用

一般来说 let b:{name:string}；这样用，表示我们声明一个b对象且一定要有一个string类型的name属性，如果没有就会报错

```js
let b : {name:string}
b={};//会报错，name is missing
b={name:"achacha"}//通过
b={name:"achacha",age:24}//也会报错 因为多了age属性 如果想要，那么可以在上面声明的时候加个age let b:{name:'...',age:'..'};

//在属性声明后面加个？ 就表示这个属性有也行没有也行
let b:{name:string , age?:number};
      b={name:'acha',age:18}//不报错
       b={name:'acha'}//不报错
 //当我们需要只有某个属性是必须有，其他属性都是可有可无的时候，可以通过这种方式来实现
       let c :{name:string , [propName: string]:any};//propName只是个形式，可以是别的变量名，string是限制属性名是个string类型，这里面就表示任意类型的以string为名字的任意属性
       //这里还要注意[propName:string]:any 这里是对索引进行格式声明 而前面name已经有了string，所以这里的下标设置要不就是string 要不就是any，因为如果这里在把[propName:string]:number 后面设置成number，而name是在propName这个属性数组里面的，而name:string就和number不符合 就会报错
 //这样就可以让除了name属性是必须的，可以任意添加其他附加属性，但是没有name还是会报错
       
```



### 10.array [1,2,3] 任意js数组

### 11、tuple  [4,5]   元素，TS新增类型，固定长度的数组

### 12、enum enum{a,b}   枚举，ts新增的类型 



### ts中函数返回值

```tsx
function fn():string |number{
    //这个就表示函数的返回值一定是string类型或者number类型
}
//如果函数没有返回值
function fn():void {
    //这里如果谢了return +任何值都会报错
    //但是 return； 或者 return null ; 或者 return undefined； 都不会报错
}

function fn():never{
    //never表示 永远不会返回结果，这个就只要有return都会报错
    //一般这个函数都是用来报错的
    throw new Error('报错了！');
}

```

#### 定义函数的结构

```js
let d:(a:number,b:number)=>number;
//这样声明表示只有a,b两个数字类型参数 且返回值也一定要是number类型
d = function (n1:number,n2:number):number{
    return n1+n2;
}
```

#### 定义数组结构

```js
let e: string[];//表示字符串类型数组
e =['a','b','c'];
let f: number[];//表示数字类型的数组
//或者
let g: Array<number>;//也是表示数字类型数组

```

### 元组----固定长度的数组

```js
let h:[string,string];//表示两个string元素的数组
h = ['hello','abc'];//多了或者少了或者格式不对了都要报错
let n:[string,number]//长度为2 第一个元素为string，第二个元素是Number

```



### Enum类型（枚举类型）

一般来说字符串比较大，当我们想减小数据库存储压力

```tsx
let i:{name:string, gender: 0|1};
//我们想让0表示男 1表示女
i={
    name:"achacha",
    gender:0
}
//但是我们自己倒是知道0和1代表什么意思，但是如果这是给用户的话，用户可不知道，所以我们可以用到enum类型
enum Gender = {
    Male = 0,
    Female =1
}

i={
    name:'achacah',
    gender:Gender.Male
}
console.log(i.gender === Gender.Male);
```



### 两个类型间可以用‘   |   ’或者‘  & ’ 来连接 | 表示或者 &表示同时

let j : string|number; 表示j可以是数值或者字符串类型的

let p : {name:string} & {age : number}; 表示 p对象必须有这两个属性

### 类型别名

```js
type mytype = string;
let m:mytype //就相当于let m:string

//一般是用于这种情况
type mytype = 1|2|3|4|5;//表示值只能是12345
let k:mytype;
let p:mytype;
```





