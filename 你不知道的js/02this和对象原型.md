[TOC]

#  this和对象原型

## 第一章 关于this

### 1.1 为什么要用this

this 提供了一种更优雅的方式来隐式“传递”一个对象引用，因此可以将 API 设计 得更加简洁并且易于复用。显式传递上下文对象会让代码变得越来越混乱，使用 this 则不会这样。

### 1.2 误解 

#### 1.2.1 指向自身

除了函数对象，还有更多**更适合**存储状态的地方。 **函数表达式需要引用自身时（比如使用递归，或者定义自己的属性和方法），应当要给函数表达式具名，而不是使用arguments.callee（已经被弃用和批判的用法）。**

```JavaScript
function foo(num) {     
	console.log( "foo: " + num );    
	this.count++;  // 记录 foo 被调用的次数
     // 记录 foo 被调用的次数改为     
    // foo.count++;，可以得到正确答案4,但回避了this问题
} 
foo.count = 0; 
var i; 
for (i=0; i<10; i++) {
	if (i > 5) {         
		foo( i );     
          // 使用 call(..) 可以确保 this 指向函数对象 foo 本身
        // foo.call( foo, i ); 
	} 
} // foo: 6 // foo: 7 // foo: 8 // foo: 9 

// foo 被调用了多少次？ 
console.log( foo.count ); // 0 -- WTF?
```

​	*执行 foo.count = 0 时，的确向函数对象 foo 添加了一个属性 count。但是函数内部代码 **this.count 中的 this 并不是指向那个函数对象**，所以虽然属性名相同，根对象却并不相 同，困惑随之产生。

如果要从函数对象内部引用它自身，那只使用 this 是不够的。一般来说你需要通过一个指 向函数对象的词法标识符（变量）来引用它。



#### 1.2.2 它的作用域

**this在任何情况下都不指向函数的词法作用域。**

作用域“对象”无法通过 JavaScript 代码访问，它存在于 JavaScript 引擎内部。

### 1.3 this到底是什么

this是在运行时绑定的，而不是在编写时绑定，它的上下文取决于函数调用时的各种条件。this 的绑定和函数声明的位置没有任何关系，this只取决于函数的调用方式。

> 当一个函数被调用时，会创建一个活动记录（有时候也称为**执行上下文**）。这个记录会包 含函数在哪里被调用**（调用栈）、函数的调用方法、传入的参数等**信息。this 就是记录的 其中一个属性，会在函数执行的过程中用到。

## 第二章 this全面解析

### 2.1 调用位置

调用位置是函数在代码中被调用的位置，最重要的是要**分析调用栈**。 比如

```JavaScript
function baz(){
    // 当前调用栈是baz，调用位置是全局作用域
    console.log(this); // window || global
    bar();
}

function bar(){
    // 当前调用栈是baz -> bar，调用位置在baz中
    // 可以拿到baz词法作用域的变量，但是和this无关
    console.log(this); // window || global
    foo();
}

function foo(){
    // 当前调用栈是baz -> bar -> foo，调用位置在bar中
    // 可以拿到bar和baz词法作用域的变量，但是和this无关
    console.log(this) // window || global
}
baz(); // baz的调用位置
```

### 2.2 绑定规则

#### 2.2.1 默认绑定

**this默认指向全局对象。** 严格模式下，如果this找不到具体对象，就是undefined。 但如果函数声明的区域不是严格模式的，即使在严格模式下调用，也不影响它将默认的this指向全局。

```JavaScript
function foo() {     
	console.log( this.a ); 
} 
var a = 2; 
foo(); // 2
//当调用 foo() 时，this.a 被解析成了全局变量 a。函数调用时应用了 this 的默认绑定，因此 this 指向全局对象。
//。在代码中，foo() 是直接使用不带任何修饰的函数引用进行调用的，因此只能使用 默认绑定，无法应用其他规则。

//如果使用严格模式（strict mode），那么全局对象将无法使用默认绑定
//此 this 会绑定 到 undefined：
function foo() {      
    "use strict"; 
     console.log( this.a ); 
} 
 var a = 2; 
 foo(); // TypeError: this is undefined
```



#### 2.2.2 隐式绑定

this隐式绑定的，是整个调用链上，处于方法**上一层的对象**。

```javascript
function foo(){
    console.log(this.a)
}

var obj = {
    a: 2,
    foo: foo,
};

obj.foo(); //2
//其实等效于，JS的函数调用其实更像是一种语法糖
obj.foo.call(obj);
//当函数引 用有上下文对象时，隐式绑定规则会把函数调用中的 this 绑定到这个上下文对象。因为调 用 foo() 时 this 被绑定到 obj，因此 this.a 和 obj.a 是一样的。
```

**隐式丢失**

需要特别注意的是，**回调函数丢失this绑定**是非常常见的

```JavaScript
function foo() {     
	console.log( this.a );
} 
function doFoo(fn) {     
    // fn 其实引用的是 foo 
	fn(); // <-- 调用位置！
} 
var obj = {     
    a: 2,     
    foo: foo  
}; 
var a = "oops, global"; // a 是全局对象的属性 
doFoo( obj.foo ); // "oops, global"
//参数传递其实就是一种隐式赋值，因此我们传入函数时也会被隐式赋值
```

因为调用它的操作通常不包含隐式绑定和显式绑定。 这个时候可以选择：

- ES6的lambda表达式
- 从外部提供`var _this = this`变量来注入进去
- bind显式绑定

#### 2.2.3 显式绑定

call和apply的效果一样，传入参数的方式不一样。 call从第二个参数开始，逐个传入。 apply的第二个参数是一个Array，参数放在Array里。 <u>bind的参数形式类似于call，但是返回的是显式绑定后的函数对象，需要后续去调用执行</u>。 **bind常用于柯里化一个函数对象。**

**硬绑定**

创建了函数 bar()，并在它的内部手动调用 了 foo.call(obj)，因此强制把 foo 的 this 绑定到了 obj。无论之后如何调用函数 bar，它 总会手动在 obj 上调用 foo。这种绑定是一种显式的强制绑定，因此我们称之为硬绑定。

【硬绑定的典型应用场景】就是创建一个**包裹函数**，传入所有的参数并返回接收到的所有值；另一种使用方法是创建一个 i 可以重复使用的辅助函数

```JavaScript
function foo(something) {     
    console.log( this.a, something );     
    return this.a + something; 
} 
var obj = {  a:2 }; 
var bar = function() {     
    return foo.apply( obj, arguments ); 
}; 

var b = bar( 3 ); // 2 3 
console.log( b ); // 5
```



```JavaScript
// ES6写法
Function.prototype._bind = function(thisArg, ...args){
    return (...newArgs) => this.call(thisArg, ...args, ...newArgs);
}

// ES5写法
Function.prototype._bind = function(thisArg){
    var args = [].slice.call(arguments, 1);
    var fn = this;
    return function(){
        var newArgs = [].slice.call(arguments, 0);
        return fn.apply(thisArg, args.concat(newArgs));
    };
}

//MDN提供的polyfill
if(!Function.prototype.bind) {
    Function.prototype.bind = function(oThis) {
        // 类型判断
        if(typeof this !== 'function'){
            throw new TypeError('Function.prototype.bind - what is trying to be found is not callable');
        }
        
        var aArgs = Array.prototype.slice.call(arguments, 1),
            fToBind = this,
            fNOP = function(){},
            fBound = function(){
                return fToBind.apply((
                    this instanceof fNOP && oThis? this: oThis
                ),
                aArgs.concat(Array.prototype.slice.call(arguments))
                );
            };
        
        // 继承原型链
        fNOP.prototype = this.prototype;
        fBound.prototype = new fNOP();

        return fBound;
    }
}
```

#### 2.2.4 new绑定

实际上并不存在所谓的“构造函数”，只有对于函数的“构造调用”。

用new来调用函数，会自动执行下面的操作：

1. 创建（或者说构造）一个全新的对象。
2. 这个新对象会被执行[[Prototype]]链接。
3. 这个新对象会绑定到函数调用的this。
4. 如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。

```JavaScript
function foo(a) {      
    this.a = a;
}  
var bar = new foo(2); 
console.log( bar.a ); // 2
//new 来调用 foo(..) 时，我们会构造一个新对象并把它绑定到 foo(..) 调用中的 this 上
```



### 2.3 优先级

- new在调用构造函数的时候做初始绑定。
- 显式绑定或硬绑定优先级最高。
- 没有显式绑定，就使用隐式绑定。
- 如果都没有，就使用默认绑定（undefined，是不是全局看严格模式）。

### 2.4 绑定例外

#### 2.4.1 被忽略的this (空对象)

显式绑定中，如果对this不敏感，可以传入`null`，但是可能有副作用。 **更安全的做法是，传入一个空对象**，即`var empty = Object.create(null)`，它连指向Object.prototype的__proto__都没有，比{}更空。

```javascript
function foo(a,b) {     
    console.log( "a:" + a + ", b:" + b ); 
} 

// 我们的 DMZ 空对象 
var ø = Object.create( null ); 

// 把数组展开成参数 
foo.apply( ø, [2, 3] ); // a:2, b:3 

// 使用 bind(..) 进行柯里化
var bar = foo.bind( ø, 2 );  bar( 3 ); // a:2, b:3
//使用变量名 ø 不仅让函数变得更加“安全”，而且可以提高代码的可读性，因为 ø 表示 “我希望 this 是空”，这比 null 的含义更清楚。
```



#### 2.4.2 间接引用

`(p.foo = o.foo)()`这里返回的引用是foo，而不是p.foo，严格模式下，它的this指向会指向undefined。

#### 2.4.3　软绑定

硬绑定会大大降低函数的灵活性，使 用硬绑定之后就无法使用隐式绑定或者显式绑定来修改 this。

**软绑定**，可以实现和硬绑定相 同的效果，同时保留隐式绑定或者显式绑定修改 this 的能力。

```JavaScript
if (!Function.prototype.softBind) {     
    Function.prototype.softBind = function(obj) {         
        var fn = this;        
        // 捕获所有 curried 参数         
        var curried = [].slice.call( arguments, 1 ); 
        var bound = function() {            
            return fn.apply(                
                (!this || this === (window || global)) ?    
                obj : this    
                curried.concat.apply( curried, arguments ) 
            );       
        };       
        bound.prototype = Object.create( fn.prototype );  
        return bound;      
    }; 
}
```



### 2.5 this词法

lambda表达式（箭头函数）的this是**根据外层作用域来决定**。 lambda表达式常用于回调函数，比如eventListener和setTimeout操作中。

```JavaScript
function foo() {
    // 返回一个箭头函数
    return (a) => {
        // 这里的this其实是foo中的this
        console.log(this.a);
    };
}

var obj1 = {a:2},
    obj2 = {a:3};

// bar的this指向foo的this，foo的this此时显式绑定了obj1
var bar = foo.call(obj1);
// 虽然显式绑定了bar的this，但是这种方法对箭头函数不起作用
// 它的this依然指向先前foo被显式绑定的obj1
bar.call(obj2); //2
```

**箭头函数最常用于回调函数中，例如事件处理器或者定时器**

> ES6 中的箭头函数并不会使用四条标准的绑定规则，而是根据当前的词法作用域来决定 this，具体来说，箭头函数会继承外层函数调用的 this 绑定（无论 this 绑定到什么）。这 其实和 ES6 之前代码中的 **self = this** 机制一样。

## 第三章 对象

### 3.1 语法

```
//文字语法（声明形式）（创建简单对象时推荐使用）
var myObj = {
    key: value,
};

//构造形式
var myObj = new Object();
myObj.key = value;
```

### 3.2 类型

JS一共有七种主要类型（语言类型）：

- string
- number
- boolean
- undefined
- null
- object
- symbol

> **注意，**简单基本类型（string、boolean、number、null 和 undefined）本身并不是对象。 null 有时会被当作一种对象类型，但是这其实只是语言本身的一个 bug，即**对 null 执行 typeof null 时会返回字符串 "object"**。1 实际上，null 本身是基本类型。
>
> **❤ 注 1：** 原理是这样的，不同的对象在底层都表示为二进制，在 JavaScript 中二进制前三位都为 0 的话会被判 断为 object 类型， null 的二进制表示是全 0，自然前三位也是 0，所以执行 typeof 时会返回“object”。 

```JavaScript
console.log(typeof null); //object
console.log(typeof string);  //undefined
console.log(typeof number); //undefined
console.log(typeof String); //function
console.log(typeof Number);//function
```

内置对象（注意第一个字母大写，这些都是构造函数）：

- String
- Number
- Boolean
- Object
- Function
- Array
- Date
- RegExp
- Error

> 在 JavaScript 中，它们实际上只是一些**内置函数**。这些内置函数可以当作**构造函数** （由 new 产生的函数调用——参见第 2 章）来使用，从而可以**构造一个对应子类型的新对 象**。

### 补充：js[中的基本类型和引用类型](D:\文件\前端\笔记\你不知道的js\js中的基本类型和引用类型.md)

```javascript
var strPrimitive = "I am a string";  
typeof strPrimitive; // "string"  
strPrimitive instanceof String; // false 
//typeof:确定变量是字符串、数值、布尔值还是undefined的最佳工具。
//instanceof ：判断是否是某个对象类型。
var strObject = new String( "I am a string" );  
typeof strObject; // "object" 
strObject instanceof String; // true 

// 检查 sub-type 对象 
Object.prototype.toString.call( strObject ); // [object String]
```

- 在必要时**语言会自动把字符串字面量转换成一个 String 对象**（如获取长度、访问某个字符等），也就是说你并不需要 显式创建一个对象。JavaScript 社区中的大多数人都认为能使用文字形式时就不要使用构 造形式。
- 如果使用类似 42.359.toFixed(2) 的方法，引擎会把对象 42 转换成 new Number(42)。对于布尔字面量来说也是如此。
- null 和 undefined 没有对应的构造形式，它们只有文字形式。相反，Date 只有构造，没有 文字形式。
- 对于 Object、Array、Function 和 RegExp（正则表达式）来说，无论使用文字形式还是构 造形式，它们都是对象，不是字面量。
- Error 对象很少在代码中显式创建，一般是在抛出异常时被自动创建。也可以使用 new Error(..) 这种构造形式来创建，不过一般来说用不着。 

### 3.3 内容

- 对象的内容是由一些存储在特定命名位置的值组成的，我们称之为**属性**。 
- 存储在对象容器内部的是这些属性的名称，它们就像指针一样，指向这些值真正的存储位置。 
- **`.key`方式称为属性访问，`['key']`方式称为键访问**。 属性访问更直接和直观，但是其属性名有一定的规范；键访问允许传入动态的字符串变量来访问，只要是字符串即可，更灵活。
- 在对象中，属性名永远都是字符串。如果你使用 string（字面量）以外的其他值作为属性 名，那它首先会被转换为一个字符串。即使是数字也不例外

#### 3.3.1 可计算属性名

ES6支持在键访问中传入一个表达式来当作属性名，比如`myObj[prefix + 'foo']`。在文字形式中使用 [] 包裹一个表达式来当作属性名

```JavaScript
var prefix = "foo"; 
var myObject = {    
    [prefix + "bar"]:"hello",   
}; 
myObject["foobar"]; // hello 
```

#### 3.3.2 属性与方法

属于对象的函数通常称为方法。

即使你在对象的文字形式中声明一个函数表达式，这个函数也不会“属于”这个对象—— 它们只是对于相同函数对象的多个引用

#### 3.3.3 数组

数组通过数字下标[索引]访问。 数组可以添加命名属性，但是不会改变其length值。

```JavaScript
var myArray = [ "foo", 42, "bar" ];  
myArray.baz = "baz";  
myArray.length; // 3 
myArray.baz; // "baz"
```

**注意：**如果你试图向数组添加一个属性，但是属性名“看起来”像一个数字，那它会变成 一个数值下标（因此会修改数组的内容而不是添加一个属性）：

```JavaScript
var myArray = [ "foo", 42, "bar" ];  
myArray["3"] = "baz";  
myArray.length; // 4 
myArray[3]; // "baz"
```

#### 3.3.4 复制对象

- 对于JSON安全的对象来说，可以使用

  `var cloned = JSON.parse(JSON.stringify(target))`的方式。

  当然，这种方法需要保证对象是 JSON 安全的，所以只适用于部分情况。

-  ES6中可以使用Object.assign(目标对象,源对象)方法（浅复制）

  由于 Object.assign(..) 就是使用 = 操作符来赋值，所 以源对象属性的一些特性（比如 writable）不会被复制到目标对象。

```JavaScript
const cloned = Object.assign(
        // 生成原型链
        Object.create(Object.getPrototypeOf(target)), 
        target
    );
```

需要注意的是，PropertyDescriptor不会被按原样复制，而是保持默认值。

#### 3.3.5 属性描述符

从 ES5 开始，所有的属性都具备了属性描述符。

```JavaScript
var myObject = { a:2 }; 
Object.getOwnPropertyDescriptor( myObject, "a" );  
 { 
    value: 2, 
    writable: true, //writable（可写）
    enumerable: true, //enumerable（可枚举）
    configurable: true // configurable（可配置）
}
//也可以使用 Object.defineProperty(..) 来添加一个新属性或者修改一个已有属性（如果它是 configurable）并对特性进行设置
var myObject = {}; 
 Object.defineProperty( myObject, "a", {     
     value: 2,     
     writable: true,     
     configurable: true,      
     enumerable: true
 } ); //只要属性是可配置的，就可以使用 defineProperty(..) 
```

一个descriptor有6个可能的属性，同时只能拥有其中4个（分为基本类型和引用类型）：

- **value**，属性值（只能用于基本类型）
- **writable**，是否可以写入，false表示readonly，默认true（**只能用于基本类型**）,如果在严格模式下，写入操作会报错TypeError
- **get，getter**方法（只能用于引用类型）
- **set，setter**方法（只能用于引用类型）
- **configurable**，descriptor是否可以**修改**（或者是**删除**），默认true。<u>把 configurable 修改成 false 是单向操作，无法撤销！</u>（一个小小的例外：即便属性是 configurable:false， 我们还是可以 把 writable 的状态由 true 改为 false，但是无法由 false 改为 true。）
- **enumerable**，是否为可枚举属性，关系到能否出现于for...of、for...in等操作中，默认true

#### 3.3.6 不变性

1. **结合`writable: false`和`configurable: false`**就可以创建一个真正的常量属性。

```
var myObject = {};

Object.defineProperty( myObject, 'Favorite_Number', {
    value: 22,
    writable: false,
    configurable: false,
});
```

1. 禁止扩展，使用Object.preventExtensions( targetObject )，不能再添加新属性。（在严格模式下，添加新属性将会抛出 TypeError 错误。）
2. 密封，使用Object.seal( targetObject )，只能修改值，不能添加/删除或重新配置属性。（实际上会在一个现有对象上调用 Object.preventExtensions(..) 并把所有现有属性标记为 configurable:false。）
3. 冻结，使用Object.freeze( targetObject )，在seal基础上在将属性改为`writable: false`，只读。（**“深度冻结”**一个对象，具体方法为，首先在这个对象上调用 Object.freeze(..)， 然后遍历它引用的所有对象并在这些对象上调用 Object.freeze(..)。）

#### 3.3.7 [[Get]]

- 当给对象进行一次属性访问时（如：myObject.a ），其实是使用了[[Get]]操作（有点像函数调 用：\[[Get]]()）。 
- 对象默认的Get操作首先在对象中查找是否有名称相同的属性，如果找到就返回；
- 没有找到，就会去原型链上寻找；
- 如果无论如何都没有找到名称相同的属性，那 [[Get]] 操作会返回值 undefined

#### 3.3.8 [[Put]]

给对象的属性赋值会触发 [[Put]] 来设置或者创建这个属性（实际情况复杂）。

如果属性已经存在，Put会检查：

1. 属性是否是访问描述符，如果是，并且有setter，就调用setter。
2. 属性的数据描述符中writable是否为false？如果为false，非严格模式下静默失败，严格模式抛出错误。
3. 如果都不是，将该值设置为属性的值。
4. 如果对象中不存在这个属性，[[Put]] 操作会更加复杂。

#### 3.3.9 Getter和Setter

在 ES5 中可以使用 getter 和 setter 部分改写默认操作，但是只能应用在单个属性上，无法 应用在整个对象上。

getter 是一个隐藏函数，会在获取属性值时调用。setter 也是一个隐藏 函数，会在设置属性值时调用。

> 当一个属性定义 getter、setter 或者两者都有时，这个属性会被定义为**“访问描述 符”**（和“数据描述符”相对）。对于访问描述符来说，**JavaScript 会忽略它们的 value 和 writable 特性，取而代之的是关心 set 和 get**（还有 configurable 和 enumerable）特性。

定义方式

```JavaScript
// 直接式
var myObject = {
    // 给a定义一个getter，此时再去访问a，只会获得2
    get a(){
        return 2;
    },
    // 不是单纯的setter，而会取两倍的值
    set a(val){
        this._a_ = val * 2;
    }
    //即便有合法的 setter，由于我们自定义的 getter 只会返回 2，所以 set 操作是 没有意义的。
}


// descriptor式
Object.defineProperty(myObject, 'b', {
    get: function(){
        return this._b_;
        //名称 _b_ 只是一种惯例，没有任何特殊的行为——和其他普通属性 一样。
    },
    set: function(val){
        this._b_ = val*2;
    },
    enumerable: true,//确保b会出现在对象的属性列表中
});
myObject.b = 2
console.log(myObject.b)//4
```

#### 3.3.10 存在性(如何区分undefined)

- 如何区分 myObject.a 的属性访问返回值： undefined
- 1. 是属性中存储的 undefined
  2. 是因为属性不存在

```javascript
("a" in myObject); // true 
("b" in myObject); // false  

myObject.hasOwnProperty( "a" ); // true 
myObject.hasOwnProperty( "b" ); // false
```

- `in`操作符会检查属性是否在对象及其[[Prototype]]原型链中。需要注意的是，**`in`只检查键是否存在**，比如在Array中，并不会检查Array的内容，而是检查下标。
-  hasOwnProperty只会检查属性是否在对象中，**不会检查原型链**。

#### 3.3.11 可枚举性

可枚举，相当于可以出现在对象属性的遍历中。 **propertyIsEnumerable**会检查给定的属性名是否直接存在于对象中(而不是原型链中)，并且满足enumerable: true。

```JavaScript
var myObject = { };  

Object.defineProperty(     
    myObject,     "a",    
    // 让 a 像普通属性一样可以枚举     
    { enumerable: true, value: 2 }
); 

Object.defineProperty( 
    myObject,     "b",    
    // 让 b 不可枚举     
    { enumerable: false, value: 3 } ); 

myObject.b; // 3 
("b" in myObject); // true  
myObject.hasOwnProperty( "b" ); // true 
//in 和 hasOwnProperty(..) 的区别在于是否查找 [[Prototype]] 链
 
for (var k in myObject) {     
    console.log( k, myObject[k] ); 
} // "a" 2
//myObject.b 确实存在并且有访问值，但是却不会出现在 for..in 循环中

myObject.propertyIsEnumerable( "a" ); // true 
myObject.propertyIsEnumerable( "b" ); // false  

Object.keys( myObject ); // ["a"] 
//Object.keys(..) 会返回一个数组，包含所有可枚举属性
Object.getOwnPropertyNames( myObject ); // ["a", "b"]
//Object.getOwnPropertyNames(..) 会返回一个数组，包含所有属性，无论它们是否可枚举。
```



### 3.4 遍历

- for...in：遍历所有可遍历的**属性名**（包括 [[Prototype]] 链）（不是值）。
- for...of：遍历所有可遍历**属性的值**。(ES6新增)，数组有内置的 @@iterator，因此 for..of 可以直接应用在数组上（见下例）。
- foreach，every，some，map，filter，reduce等委托的原型方法则是**将遍历改为链式调用**。
- 1. forEach(..) 会遍历数组中的所有值并忽略回调函数的返回值。
  2. every(..) 会一直运行直到回调函数返回 false（或者“假”值）
  3. some(..) 会一直运行直到回调函数返回 true（或者 “真”值）。
  4. every(..) 和 some(..) 中特殊的返回值和普通 for 循环中的 break 语句类似，它们会提前 终止遍历

使用**Symbol.iterator**对象，可以用来定义对象的@@iterator内部属性：

```JavaScript
var myArray = [ 1, 2, 3 ]; 
var it = myArray[Symbol.iterator](); 

it.next(); // { value:1, done:false }  
it.next(); // { value:2, done:false } 
it.next(); // { value:3, done:false } 
it.next(); // { done:true }，这个机制和 ES6 中发生器函数的语义相 关
```

- 引用类似 iterator 的特殊属性时要使用符号名，而不是符号包含的 值。此外，虽然看起来很像一个对象，但是 @@iterator 本身并不是一个迭代 器对象，而是一个返回迭代器对象的函数——这点非常精妙并且非常重要。

和数组不同，普通的对象没有内置的 @@iterator，所以无法自动完成 for..of 遍历。简单来说，这样做是为了避免影响未来的对象 类型。，我们可以给需要遍历的对象定义@@iterator

```JavaScript
var myObject = {
    a: 2,
    b: 3,
};

Object.defineProperty(myObject, Symbol.iterator, {
    enumerable: false,
    writable: false,
    configurable: true,
    value: function() {
        var o = this;
        var idx = 0;
        var ks = Object.keys(o);
        return {
            next: function() {
                return {
                    value: o[ks[idx++]],
                    done: (idx > ks.length),
                };
            },
        };
    }
});

var it = myObject[Symbol.iterator]();
it.next(); // {value:2, done: false}
it.next(); // {value:3, done: false}
it.next(); // {value:undefined, done: true}
```

可以用for of 定义一个无限迭代器，如产生随机数等

```JavaScript
var randoms = {    
    [Symbol.iterator]: function() {        
        return {            
            next: function() {               
                return { value: Math.random()
                       };            
            }       
        };     
    } }; 

var randoms_pool = []; 
for (var n of randoms) {   
    randoms_pool.push( n ); 
// 防止无限运行！    
    if (randoms_pool.length === 100) break; 
}
```

## 第四章 混合对象“类”

面向类的设计模式：实例化(instantiation)，继承(inheritance)，（相对）多态(polymorphism)。

### 4.1 类理论

类理论包括：

- 类（Vehicle类交通工具都包含的东西，Car类对通用Vehicle的特殊化）
- 继承
- 实例化
- 多态，父类的通用行为可以被子类用更特殊的行为重写（JS中不推荐）。

#### 4.4.1 类设计模式

面向对象的设计模式有，**迭代器模式、观察者模式、工厂模式、单例模式**等。

#### 4.4.2 JavaScript中的类

JS中的类是构造函数与原型的结合，与其他语言的类不同。

### 4.2 类的机制

#### 4.2.1 建造

为了获得真正可以交互的对象，我们必须**按照类来建造**一个东西，这个东西通常被称为**实例**。有需要的话，我们可以直接在实例上调用方法并访问其所有公有数据属性。

#### 4.2.2 构造函数

类实例是由一个特殊的类方法构造的，这个方法名和类名相同，被称为构造函数。**这个方法的任务是初始化实例所需要的所有信息及属性。**

```JavaScript
function CoolGuy() {
    this.name = 'John';
}
```

构造函数需要用new来调用，这样引擎才会去构造一个新的类实例。

### 4.3 类的继承

子类需要继承父类的属性与原型方法。

#### 4.3.1 多态

子类可以重写父类方法。 **子类重写的方法在子类的prototype上，不会改变父类的prototype**，只是子类在寻找方法时，会跟随原型链，率先找到处于自身原型上的方法，从而调用。 ES6用super代指父类的构造函数，从而能让子类通过这种方式找到父类的原型及其方法。

#### 4.3.2 多重继承

JS的继承是单向的。 JS不支持多重继承，因此使用**混入模式实现**多重继承。

### 4.4 混入

JavaScript 中只有对象，并不存在可以被实例化的“类”。一个对象并不会被复制到其他对 象，它们会被关联起来。JavaScript 开发者也想出了一个方法来 **模拟类的复制行为**，这个方法就是**混入**。

#### 4.4.1 显式混入

JavaScript 中的函数无法（用标准、可靠的方法）真正地复制，所以你只能复制对共享 函数对象的引用（函数就是对象；参见第 3 章）。如果你修改了共享的函数对象（比如 ignition()），比如添加了一个属性，那 Vehicle 和 Car 都会受到影响

```javascript
// 不覆盖属性
function mixinWithoutOverwrite(source, target) {
    for (var key in source){
        // 只在不存在的情况下复制
        if( !(key in target) ){
            target[key] = source[key];
        }
    }
    return target;
}


//覆盖属性
//如果我们是先进行复制然后对 Car 进行特殊化的话，就可以跳过存在性检查。不过这种方 法并不好用并且效率更低，所以不如第一种方法常用：
function mixinWithOverwrite(source, target) {
    for (var key in source){
        target[key] = source[key];    
    }

    return target;
}

//。从技术角度来说，函数实际上没有 被复制，复制的是函数引用(对象数组)。
```

另一种变体 — 是寄生式继承，将父类的实例混入子类。它既是显式的又是隐式的

```JavaScript
function Super() {
    this.name = 'father';
}
Super.prototype.say = function() {
    console.log('Method from father');
}

function Sub() {
    // 父类实例
    var father = new Super();
    father.name = 'son';
    // 保存引用
    var extendedSay = father.say;
    // 多态重写
    father.say = function() {
        extendedSay.call(this);
        console.log('Method from son');
    }
    return father;
}
// 调用时无需用new。是因为我们没有使用这个对象而是返回了我们自己的 car 对象，所 以最初被创建的这个对象会被丢弃，因此可以不使用 new 关键字调用 Car()，可以避免创建并丢弃多余的对象
var son = Sub();
```

#### 4.4.2隐式混入

隐式混入其实就是把其他对象的方法拿过来使用，**使用call、apply、或bind，将其this指针指向自身。**（尽量避免使用这样 的结构，以保证代码的整洁和可维护性。）

```JavaScript
var landLord = {
    name: 'himself',
    checkMoney: function() {
        console.log('Landlord is giving money to ' + this.name);
    },
}

var robinHood = {
    name: 'Robin Hood',
    rob: function() {
        // 把其他对象的方法拿过来使用
        landLord.checkMoney.call(this);
    },
}

robinHood.rob(); //Landlord is giving money to Robin Hood
```

## 第五章 原型

### 5.1 [[Prototype]]

几乎所有的对象在创建时 [[Prototype]] 属性都会被赋予一个非空的值。

对于默认的Get操作来说，如果无法在对象本身找到需要的属性或方法，就会继续访问对象的Prototype链。

```JavaScript
var another = {
    a:2
};
//创建一个关联到anotherObject的对象
var myObj = Object.create(another);
// 能找到，但是在myObj.__proto__上找到
myObj.a; //2
// Object.create(..)会创建一个对象并把这个对象的 [[Prototype]] 关联到指定的对象。
```

- 使用for...in遍历对象的原理和查找原型链相似。
- 使用 in 操作符来检查属性在对象 中是否存在时，同样会查找对象的整条原型链（无论属性是否可枚举）

#### 5.1.1 Object.prototype

**一般原型链都会最终指向Object.prototype**，而**Object.prototype的`__proto__`为null**。

#### 5.1.2 属性设置和屏蔽

- 如果**原型链上找不到某个属性，该属性就会被直接添加到调用对象上。** 

- 如果属性既出现在对象中，也出现在其原型链中，那么对象中的属性就会屏蔽原型链上层所有的同名属性。 优先顺序为；**对象实例属性 > 下层原型 > 上层原型**。

-  Set的情况则更为复杂，比如myObj自身没有foo，但`myObj.foo = 'bar'`的情况：

- 1. 如果原型链上层存在名为foo的属性，且可以写入，那么就会直接在myObj中添加一个名为foo的新属性，并且赋值进去。

- 2. 如果原型链上层存在foo，但它是只读，那么就无法进行修改，非严格模式下被忽略，严格模式下抛出错误。（这样做主要是为了模拟类属性的继承。）

- 3. 如果原型链上层存在foo，且是一个setter，那么就会调用这个setter。此时foo不会添加到myObj，也不会改变这个setter。

     （*如果你希望在第二种和第三种情况下也屏蔽 foo，那就不能使用 = 操作符来赋值，而是使 用 Object.defineProperty(..)（参见第 3 章）来向 myObject 添加 foo。*）

使用屏蔽得不偿失，所以应当尽量避免使用

- ！！有些情况下会**隐式产生屏蔽**，一定要当心。

```JavaScript
var anotherObject = {  a:2 }; 
var myObject = Object.create( anotherObject );  
anotherObject.a; // 2 
myObject.a; // 2  
anotherObject.hasOwnProperty( "a" ); // true 
myObject.hasOwnProperty( "a" ); // false  

myObject.a++; // 隐式屏蔽！ 

myObject.a; // 3 
myObject.hasOwnProperty( "a" ); // true
```

-  ++ 操作相当于 myObject.a = myObject.a + 1。因此 ++ 操作首先会通过 [[Prototype]] 查找属性 a 并从 anotherObject.a 获取当前属性值 2，然后给这个值加 1，接着用 [[Put]] 将值 3 赋给 myObject 中新建的屏蔽属性 a，天呐！

### 5.2 类

#### 5.2.1 类函数

所有函数默认都会拥有一个名为prototype的公有且不可枚举属性，它其实指向另一个对象。 同一个构造函数，及其构造的实例，它们对该函数prototype的引用其实指向同一段内存地址，修改这个prototype的属性及方法，会影响到这个构造函数及其所有实例。

#### 5.5.2 构造函数

prototype默认有一个公有且不可枚举的属性constructor，这个属性引用的是对象关联的函数。

```javascript
function foo() {
    // ...
}
foo.prototype.constructor === foo; // true
var a = new foo();
// 并不是a有这个属性，它是在原型链上找到的
a.constructor === foo; // true
```

需要注意的是，构造函数本身还是函数，new操作符只是将其调用方式变成“构造函数调用”，本质上还是需要去执行它。

在 JavaScript 中对于“构造函数”最准确的解释是，所有带 new 的函数调用。

#### 5.2.3 技术

prototype中的constructor引用是非常不可靠，且不安全的，要避免使用。.constructor 并不是一个不可变属性。它是不可枚举的，但是它的值 是可写的（可以被修改）。

实例对象 并没有 .constructor 属性，所以它会委托`__proto__`链上的 Foo. prototype。

实际上，Foo 和你程序中的其他函数没有任何区别。**函数本身并不是构造函数**，然而，当 你在普通的函数调用前面**加上 new 关键字之后，就会把这个函数调用变成一个“构造函数 调用”**。

new 会劫持所有普通函数并用构造对象的形式来调用它。

#### 5.3 （原型）继承

要创建一个合适的关联对象，我们必须使用 **Object.create(..)** 而不是使用具有副 作用的 Foo(..)。会创建一个新对象，再赋予当前对象

> **bar = Object.create( foo );** 中
>
> Object.create(..)，会创建一个新对象给bar，但是此时bar中并没有内容，还是指向的foo，可以引用foo中的属性；当foo中参数被修改时，bar的引用也会被修改；
>
> 但是当bar重写属性时，在当前实例中创建这个属性，重写的值不会改变foo的值！！！此后foo再次改变此属性不会再影响bar的属性值

ES6 添加了辅助函数 **Object.setPrototypeOf(..)**，可以用标准并且可靠的方法来修 改关联。

```javascript
// ES6 之前需要抛弃默认的 
Bar.prototype Bar.ptototype = Object.create( Foo.prototype ); 
 
// ES6 开始可以直接修改现有的
Bar.prototype Object.setPrototypeOf( Bar.prototype, Foo.prototype )
```

a instanceof Foo; // true
**instanceof** 操作符的左操作数是一个普通的对象，右操作数是一个函数。**instanceof 回答 的问题是：**在 a 的整条 [[Prototype]] 链中是否有指向 Foo.prototype 的对象？

反过来操作就是：Foo.prototype.isPrototypeOf(a)：foo是否出现在a的原型链中

```javascript
//ES5
function Super(){
    this.name = 'father';
}
Super.prototype.say = function() {
    console.log('I am ' + this.name);
};

function Sub(){
    // 执行父类构造函数，获得属性
    Super();
    // 添加或覆盖属性
    this.name = 'son';
}
// 形成原型链
Sub.prototype = Object.create(Super.prototype);
// 修复constructor指向
Sub.prototype.constructor = Sub;

// 或者更直接的原型链
Sub.prototype.__proto__ = Super.prototype;
//ES6提供一种新的操作方式
Object.setPrototypeOf(Sub.prototype, Super.prototype);


// ES6
class A {
    constructor(){
        this.name = 'father'
    }
    say() {
        console.log('Method from father: I am ' + this.name);
    }
}
class B extends A {
    constructor(){
        super();
        this.name = 'son'
    }
    say() {
        console.log(`Method from son: I am ${this.name}.`);
        super.say();
    }
}
```

另一个需要注意的是非标准的__proto__，它其实是一套getter/setter。

```javascript
Object.defineProperty(Object.prototype, "__proto__", {
    get: function() {
        return Object.getPrototypeOf(this);
    },
    set: function(o) {
        Object.setPrototypeOf(this, o);
        return o;
    },
});
```

### 5.4 对象关联

#### 5.4.1 创建关联

```javascript
//Object.create()的polyfill
if(!Object.create){
    Object.create = function(o) {
        function F(){}
        F.prototype = o;
        return new F();
    };
}
```

#### 5.4.2 关联关系是备用？

原型的实现应当遵循委托设计模式，API在原型链上出现的位置要参考现实中的情况。

## 第六章 行为委托

### 6.1 面向委托的设计

#### 6.1.1 类理论

类设计模式鼓励你在继承时使用方法重写和多态。许多行为可以先抽象到父类，然后再用子类进行特殊化。

#### 6.1.2 委托理论

首先定义对象，而不是类。通过Object.create()来创建委托对象，赋值给另一个对象，从而让该对象出现在另一个对象的原型链上。 比如`var bar = Object.create(foo);`，使得`bar.__proto__===foo`，从而bar可以通过原型链获得foo的所有属性和方法。 这种设计模式被称为对象关联。委托行为意味着某些对象在找不到属性或者方法引用时，会把这个请求委托给另一个对象。 

需要注意的是，**禁止两个对象互相委托**，否则当引用一个两者都不存在的属性或方法，会产生无限递归的循环。

```JavaScript
function Foo() {} 
var a1 = new Foo(); 
a1; // Foo {} （chrome中） ； Object {} （Firefox中）
//“{} 是一个空对象，由名为 Foo 的函数构造” ,因为 Chrome 会动态跟踪并把 实际执行构造过程的函数名当作一个内置属性，但是其他浏览器并不会跟踪这些额外的信息
```

#### 6.1.3 比较思维模型

对象关联风格相对于类风格更为简洁，因为它只关注对象之间的关联关系。

```JavaScript
Foo = {
    init: function(who) {
        this.me = who;
    },
    identify: function() {
        return 'I am ' + this.me;
    },
}
Bar = Object.create(Foo);
Bar.speak = function() {
    console.log('Hello, ' + this.identify() + '.');
};

var b1 = Object.create(Bar);
var b2 = Object.create(Bar);
// 从b1.__proto__.__proto__上得到init方法
b1.init('b1');
b2.init('b2');

// 从b1.__proto__上得到Bar的spaak方法
// 从b1.__proto__.__proto__上得到identify方法
b1.speak(); // Hello, b1.
b2.speak(); // Hello, b2.
```

#### 6.2.2 委托控件对象

委托模式可以防止上一节中不太合理的伪多态形式调用`Widget.prototype.render.call(this, $where);`。 同时init和setup两个初始化函数更具语义表达能力，同时由于将构建和初始化分开，使得初始化的时机变得更灵活，允许异步调用。 对象关联可以更好地支持**关注分离原则，创建和初始化不需要合并为一个步骤**。

### 6.3 更简洁的设计

对象关联除了能让代码看起来更简洁，更具扩展性，还可以通过委托模式简化代码结构。

### 6.4 更好的语法

ES6中使用`Object.setPrototypeOf(target, obj)`的方式来简化`target = Object.create(obj)`的写法。

### 6.5 内省

内省就是检查实例的类型。 `instanceof`实际上检查的是目标对象与构造器原型的关系，对于通过委托互相关联的对象（Foo与Bar互相关联），可以使用：

- `Foo.isPrototypeOf(Bar)`
- `Object.getPrototypeOf(Bar) === Foo`





