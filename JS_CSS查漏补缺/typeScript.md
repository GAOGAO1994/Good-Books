# TypeScript

### 二. 特点：

**１. 优点**

```
a. 可以在编译阶段就发现大部分错误，这总比在运行时候出错好
b. 同一目录下不同文件中，使用统一命名，会有命名冲突
c. 不显式的定义类型，也能够自动做出类型推论 
d. 即使 TypeScript 编译报错，也可以生成 JavaScript 文件
e. Google 开发的 Angular 就是使用 TypeScript 编写的
f. TypeScript 拥抱了 ES6 规范，也支持部分 ES7 草案的规范
    g. IDE 智能提示，配合Vscode非常好用
```

- 代码重构
- 可读性高

**２. 缺点：**

```
a.有一定的学习成本，需要理解接口（Interfaces）、泛型（Generics）、类（Classes）、枚举类型（Enums）等前端工程师可能不是很熟悉的东西
b. 短期可能会增加一些开发成本，多写一些类型的定义，长期维护的项目，TypeScript 能够减少其维护成本
```

### 三. 类型：

**１. 原始数据类型：**

```
布尔值、数值、字符串、null、undefined、symbol（表示独一无二的值）、void、any
```

**２. 内置对象类型：**

```
Boolean, Error, Array, Date, Math, RegExp
Document,HTMLElement,Event,NodeList ....  MouseEvent
```

**３. 自定类型:**

```
类、接口
```

### 四. 原始数据类型

**１. 布尔值**

```JavaScript 
let b: boolean = true
let bool: Boolean = new Boolean(true) //接口
```

**２. symbol**
表示独一无二的值

```JavaScript 
let sy: symbol =Symbol("wfa")
```

**３. null及undefined**
默认情况下null和undefined是所有类型的子类型。 就是说你可以把 null和undefined赋值给number类型的变量。

```JavaScript 
//当你指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自
let nul: null = null
let und: undefined = undefined

nul= 3 //会报错
```

**类型推论**

```JavaScript 
   // 如果初始定义为null或者undefined，未指定类型，则类型是any
    let nuls = null 或者 undefined
    uls = 3 //此时uls类型是any，可以更改
```

**４. void 空**
函数的返回值为空，则表示无return。有return，要定义其他类型

如果函数不指定类型，会进行类型推论。无return是void，有则依据返回值

```JavaScript 
function fn(): void {
    console.log(10)
}

//返回值1
function fn(): number {
    return 1
}
```

５. any 任意类型

```JavaScript 
let an: any = "a"
an = 1 //任意类型可以改变
```

### 五. 内置对象类型

**１. 获取属性**

```JavaScript 
<body>
    <div class = "box" ></div>
    <h1>good</h1>
</body>
<script src="./04-内置对象.js"></script>

let box:HTMLDivElement = document.querySelector("div">
let html:HTMLElement = document.documentElement
let html:HTMLElement = document.body

let h1:HTMLHeadingElement = document.querySelector("h1")

box.onmouseenter = funtion(e:MouseEvent):void{
    console.log()
}
```

**２. 数组**

语法：let 数组名： 类型名[ ] = 值

第一种：以在元素类型后面接上 [],示由此类型元素组成的一个数组：

```JavaScript 
let arr:number[ ] = [1,2,3,4]

//let arr4:number[] = [1,2,3,"a"] //报错："a"不是number类型
```

第二种：是使用数组泛型，Array<元素类型>

```JavaScript 
let list: Array<number> = [1, 2, 3];
```

**３. 函数**
**第一种：函数形式：**

```JavaScript 
    function 函数名( arg1:类型名, arg2:类型名): 返回值类型名{}

    function fn(a:number, b:number|string):number{return 1}

    const fn2 = function(a:number,b:number):number{ return 1 }

    const fn3 = (a:string,b:string): number => 1
```

**第二种：接口中函数的定义:**

```JavaScript 
interface 接口名称{
    //输入类型(参数): 输出类型（返回值）
    (a:number,b:number):number
}

//接口定义
interface FnInter{
    (a:number, b:number):number
}


//使用接口后相当于：
//const fn = function(a:number,b:number):number{ return 1 }
const fn:FnInter =(a,b) => 1

//调用函数
fn(1,1)
```

**４. 对象**
对象类型：依赖接口 | 类 来描述, 不给类型可以推论

a. 接口

**属性用法：**

```JavaScript 
1.不加任何符号：必传属性（不能少）
2.readonly： 只读属性（不能修改）
3.？： 可选属性
4.[propName: string]: any 任意属性 （也可以约定属性）
```

例子：

```JavaScript 
interface ObjInter {
    readonly id: number,
    name ?: string,
    age: number,
    [propName:string]: any
 
}
 
const obj:ObjInter = {
    id: 2, 
    name: "ja",
    age: 12,
    c: 1, //任意属性
    d: 3 //任意属性
}

//obj.id =3  //将会报错，因为 id是只读属性
obj.name = "good" 
```

### 六. 联合类型

**１. 数组联合**
联合类型不同于any

```JavaScript 
let arr:(number | string)[] = [1,"a",1]
let arr:(number | string | boolean)[] = [1,"a",1,true]
let list: Array<number | string> = [1, "a",3,5,"b"];
```

**２. 基本数据类型联合**

```JavaScript 
let ja： number | string =2

//ja的类型：是number联合string类型
ja="ja2"
```

### 七. 泛型

**概念：**
泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而 在使用的时候再指定类型的一种特性。

**比喻：**
女朋友/男朋友类型，当前不确定，最后才知道

第一种是，传入所有的参数，包含类型参数：

```JavaScript 
let output = identity<string>("myString");  // type of output will be 'string'
```

这里我们明确的指定了T是string类型，并做为一个参数传给函数，使用了<>括起来而不是()。

第二种方法更普遍。利用了**类型推论** -- 即编译器会根据传入的参数自动地帮助我们确定T的类型：

```JavaScript 
let output = identity("myString");  // type of output will be 'string'
```

注意我们没必要使用尖括号（<>）来明确地传入类型；编译器可以查看myString的值，然后把T设置为它的类型。

例子：

```JavaScript 
//函数泛型
    const fn =function <A,B>(a:A,b:B): A {
        return a
    }
     
    fn(1,2)
     
    
//接口泛型：
    interface FnInter<B>{
        (a:B):B
    }
    //返回值也是number类型
    const fn:FnInter<number> = ()=>1
    fn(1)
    
```

### 八. 类型断言

我们需要在还不确定类型的时候就访问其中一个类型的属性或方法
其一是“尖括号”语法：

```JavaScript 
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;

function fn(a: number | string ):boolean{
    if((<string>a).length)

}
```

另一个为as语法：

```JavaScript 
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```

两种形式是等价的。 至于使用哪个大多数情况下是凭个人喜好；然而，当你在TypeScript里使用JSX时，只有 as语法断言是被允许的。

例子 ：

```JavaScript 
function fn(a: number | string):boolean{

    //只有string类型的a2，才会true
    if((<string>a).length){
          return false
    }
    
    if((a as string).length){
        
    }
    //会报错，number类型a，不存在length属性
    //if((<number>a).length){
    //}
    
    return true
}
console.log(fn("1"))  //false
```

### 九. 类实现接口:

**1. 好处：**
a. 复用性高
b. 书写习惯

2.**比喻**：
门是一个类，防盗门是门的子类。防盗门有一个报警器的功能，给防盗门添加一个报警方法。车类，也有报警器的功能，就可以考虑把报警器提取出来，作为一个接口，防盗门和车都去实现它

**3. 例子：**
类实现多个接口：class 类 implements 接口1,接口2{}

```JavaScript 
class A implements A_InterFace,B_Interface{
    public a = 1
    public fn = (a,b) => { return 'a'}
    public b = 2
    public c = (n,m) => n+m 
}
interface A_InterFace{
    a: number,
    fn: (a:number,b:string) => string
}

interface B_Interface{
    b: number,
    c: (n:number,m:number) => number
}
```

