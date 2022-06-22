## Super关键词、抽象类、接口、属性的封装、泛型



## Super关键字

```tsx
class Animal{
    constructor(name:string){
        this.name = name;
    }
    sayHello() {
        console.log('hello~')
    }
}
class Dog extends Animal {
    sayHello(){
        super.sayHello(); //在类方法中指向当前类的父类
    }
}
```



## 抽象类

```tsx
//抽象类和其他类区别不打，只是不能被用来创建对象
//就是用来继承用的
//抽象类可以用来添加抽象方法
abstract class Animal {
    name:string;
    constructor(name:string){
        this.name = name ;
    }
    //抽象方法不用由子类来实现，在父类中只定义
    abstract sayHello():void;
}
class Dog extends Animal{
    //子类必须要重写父类的抽象方法
    sayHello(){
        console.log('hello~~~');
    }
}
```



## 接口

```tsx
//接口可以在定义类的时候去限制类的结构,且接口在使用时候不能有实际的值，接口只定义结构，不考虑实际的值
interface myInterface{
    name:string;
    age:number;
    sayHello():void
}
//接口用来定义一个类结构，用来定义一个类中应该包含哪些属性和方法，多了少了都不行
//同时接口也可以当成类型声明去使用
const obj : myInterface = {
    name:'sss',
    age:11
}
class Myclass implements myInterface{
    constructor(name:string,age:number){
        this.name = name;
        this.age = age;
    }
    sayHello(){
        console.log('hello~');
    }
}
```

#### type和interface的区别：

interface侧重对数据结构的描述，type侧重与类型的关系

多个同名interface声明可以合并，type可以定义联合类型、交叉类型、元祖和基本类型别名，通过in来迭代类型键名，生成映射类型。

## 属性的封装

```tsx
//属性是在对象中设置的，属性可以任意被修改
//属性可以任意被修改会导致对象中的数据变得非常不安全
//ts可以在属性前添加属性修饰符
class Person{
    public _name:string;
	//public可以在任意位置访问（修改）默认值
	private _age:number;
	//private 私有属性，只能在内部进行修改访问   
	//-通过在类中添加方法，使得内部的私有属性能够被外部使用
    getAge(){
        return this._age;
    }
    protected _hobit:string;
    //protected 受包含属性，只能在当前类和当前类的
}
```

```tsx
class C{
    constructor(public name:string,public age:number)
}
//等于
class C{
    constructor(name:string,age:number){
        this.name =name;
        this.age = age;
    }
}
```

## 泛型

```tsx
function fn(a:number): number{
    return a;
    //如果我们现在需要让fn返回的类型和a输入类型是一致的
}
//首先我们可以想到any
function fn(a:any):any{
    return a;
    //但是首先且不谈any的安全性问题，就算解决问题，但是我们依然不能确定a和return的类型就是一致的，只能说是any关闭了类型校验
}
//当我们在定义函数或者类时候，如果遇到了类型不明确的时候就可以使用泛型
//就是我们不清楚类型的时候就可以用使用泛型
function fn<Exam>(a:Exam):Exam{
    return a;
}
//这里就是定义了一个Exam泛型，这样就能规定规定好让类型一致
fn(a:10 ); //z这里就是return 的10就是number类型的 实现了前后一致性
//不指定泛型TS课自动对类型进行类型判断
fn<string>(a:'hello')//指定泛型、

interface Inter{
    length:number;
}
//T extends Inter 表示泛型T必须是Inter的实现类（子类）
function fn3<T extends Inter>(a: T): number{
    return a;
}

class Myclass<T>{
    name:T;
    constructor(name:T){
        this.name = name;
    }
}
const mc = new Myclass<string>('hello');
```

