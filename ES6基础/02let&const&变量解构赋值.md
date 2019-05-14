[TOC]

## let&const

### 1 let 命令

- 所声明的变量，只在`let`命令所在的代码块内有效。 

- `for`循环的计数器，就很合适使用`let`命令。 

  下面的代码如果使用`var`，最后输出的是`10`。

  ```JavaScript
  var a = [];
  for (var i = 0; i < 10; i++) {
    a[i] = function () {
      console.log(i);
    };
  }
  a[6](); // 10
  ```

  上面代码中，变量`i`是`var`命令声明的，在全局范围内都有效，所以全局只有一个变量`i`。每一次循环，变量`i`的值都会发生改变，而循环内被赋给数组`a`的函数内部的`console.log(i)`，里面的`i`指向的就是全局的`i`。也就是说，所有数组`a`的成员里面的`i`，指向的都是同一个`i`，导致运行时输出的是最后一轮的`i`的值，也就是 10。

  ```JavaScript
  var a = [];
  for (let i = 0; i < 10; i++) {
    a[i] = function () {
      console.log(i);
    };
  }
  a[6](); // 6
  ```

你可能会问，如果每一轮循环的变量`i`都是重新声明的，那它怎么知道上一轮循环的值，从而计算出本轮循环的值？**这是因为 JavaScript 引擎内部会记住上一轮循环的值，初始化本轮的变量`i`时，就在上一轮循环的基础上进行计算。

-  `for`循环还有一个特别之处，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。 

  ```JavaScript
  for (let i = 0; i < 3; i++) {
    let i = 'abc';
    console.log(i);
  }
  // abc
  // abc
  // abc
  ```

  上面代码正确运行，输出了 3 次`abc`。这表明函数内部的变量`i`与循环变量`i`不在同一个作用域，有各自单独的作用域。

#### 1.2 不存在变量提升

- `var`命令会发生“变量提升”现象，即变量可以在声明之前使用，值为`undefined`。 
- 为了纠正这种现象，`let`命令改变了语法行为，它所声明的变量一定要在声明后使用，否则报错。 *// 报错ReferenceError*

#### 1.3 暂时性死区

```JavaScript
if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```

ES6 明确规定，如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

“暂时性死区”也意味着`typeof`不再是一个百分之百安全的操作。 

另外，下面的代码也会报错，与`var`的行为不同。

```JavaScript
// 不报错
var x = x;

// 报错
let x = x;
// ReferenceError: x is not defined
```

上面代码报错，也是因为暂时性死区。使用`let`声明变量时，只要变量在还没有声明完成前使用，就会报错。

> ES6 规定暂时性死区和`let`、`const`语句不出现变量提升，主要是为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为。 

**不允许重复声明**

- `let`不允许在相同作用域内，重复声明同一个变量 

### 2 块级作用域

#### 2.1 为什么需要块级作用域

- 内置变量可能会覆盖外层变量
- 用来计数的循环变量泄露为全局变量

ES6 允许块级作用域的任意嵌套。

```JavaScript
{{{{
  {let insane = 'Hello World'}
  console.log(insane); // 报错
}}}};
```

上面代码使用了一个五层的块级作用域，每一层都是一个单独的作用域。第四层作用域无法读取第五层作用域的内部变量。

内层作用域可以定义外层作用域的同名变量。

- 块级作用域的出现，实际上使得获得广泛应用的立即执行函数表达式（IIFE）不再必要了。

- ```JavaScript
  // IIFE 写法
  (function () {
    var tmp = ...;
    ...
  }());
  
  // 块级作用域写法
  {
    let tmp = ...;
    ...
  }
  ```

#### 1.3 块级作用域与函数声明

- ES5 规定，函数只能在顶层作用域和函数作用域之中声明，不能在块级作用域声明。 （但是浏览器没有遵守这个规定，所以没有报错）
- ES6 引入了块级作用域，明确允许在块级作用域之中声明函数。ES6 规定，块级作用域之中，函数声明语句的行为类似于`let`，在块级作用域之外不可引用。 

```JavaScript
// 浏览器的 ES6 环境
function f() { console.log('I am outside!'); }

(function () {
  if (false) {
    // 重复声明一次函数f
    function f() { console.log('I am inside!'); }
  }

  f();
}());
// Uncaught TypeError: f is not a function
```

ES6 在[附录 B](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-block-level-function-declarations-web-legacy-compatibility-semantics)里面规定，浏览器的实现可以不遵守上面的规定，有自己的[行为方式](http://stackoverflow.com/questions/31419897/what-are-the-precise-semantics-of-block-level-functions-in-es6)。

- 允许在块级作用域内声明函数。
- 函数声明类似于`var`，即会提升到全局作用域或函数作用域的头部。
- 同时，函数声明还会提升到所在的块级作用域的头部。

上面的例子实际运行的代码如下。

```JavaScript
// 浏览器的 ES6 环境
function f() { console.log('I am outside!'); }
(function () {
  var f = undefined;
  if (false) {
    function f() { console.log('I am inside!'); }
  }

  f();
}());
// Uncaught TypeError: f is not a function
```

**考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。** 

- 注意：另外，还有一个需要注意的地方。ES6 的块级作用域必须有大括号，如果没有大括号，JavaScript 引擎就认为不存在块级作用域。 

### 3 const 命令

- `const`声明一个只读的常量。一旦声明，常量的值就不能改变 
- 意味着，`const`一旦声明变量，就**必须立即初始化，不能留到以后赋值。**
-  `const`的作用域与`let`命令相同：只在声明所在的块级作用域内有效、声明的常量也是不提升，同样存在暂时性死区 ，一样不可重复声明 。

**本质**

`const`实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，`const`只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了。 

*如给常量`foo`储存的是一个地址，这个地址指向一个对象。不可变的只是这个地址，即不能把`foo`指向另一个地址，但对象本身是可变的，所以依然可以为其添加新属性。* 

如果真的想将对象冻结，应该使用`Object.freeze`方法。

```JavaScript
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```

下面是一个将对象彻底冻结的函数。

```JavaScript
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, i) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```

#### 1.4 ES6 声明变量的六种方法

ES5 只有两种声明变量的方法：`var`命令和`function`命令。ES6 除了添加`let`和`const`命令，后面章节还会提到，另外两种声明变量的方法：`import`命令和`class`命令。所以，ES6 一共有 6 种声明变量的方法。

### 4 顶层对象的属性

顶层对象，在浏览器环境指的是`window`对象，在 Node 指的是`global`对象。ES5 之中，顶层对象的属性与全局变量是等价的。 （顶层对象的属性与全局变量挂钩，被认为是 JavaScript 语言最大的设计败笔之一。 ）

ES6 为了改变这一点，一方面规定，为了保持兼容性，`var`命令和`function`命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，`let`命令、`const`命令、`class`命令声明的全局变量，不属于顶层对象的属性。 （ES6 开始，全局变量将逐步与顶层对象的属性脱钩。 ）

```JavaScript
let b = 1;
window.b // undefined
```

### 5 globalThis 对象

- 浏览器里面，顶层对象是`window`，但 Node 和 Web Worker 没有`window`。
- 浏览器和 Web Worker 里面，`self`也指向顶层对象，但是 Node 没有`self`。
- Node 里面，顶层对象是`global`，但其他环境都不支持。

同一段代码为了能够在各种环境，都能取到顶层对象，现在一般是使用`this`变量，但是有局限性。

- 全局环境中，`this`会返回顶层对象。但是，Node 模块和 ES6 模块中，`this`返回的是当前模块。
- 函数里面的`this`，如果函数不是作为对象的方法运行，而是单纯作为函数运行，`this`会指向顶层对象。但是，严格模式下，这时`this`会返回`undefined`。
- 不管是严格模式，还是普通模式，`new Function('return this')()`，总是会返回全局对象。但是，如果浏览器用了 CSP（Content Security Policy，内容安全策略），那么`eval`、`new Function`这些方法都可能无法使用。

> 现在有一个[提案](https://github.com/tc39/proposal-global)，在语言标准的层面，引入`globalThis`作为顶层对象。也就是说，任何环境下，`globalThis`都是存在的，都可以从它拿到顶层对象，指向全局环境下的`this`。
>
> 垫片库[`global-this`](https://github.com/ungap/global-this)模拟了这个提案，可以在所有环境拿到`globalThis`。

## 变量的解构赋值

打破数据结构，将其拆分为更小部分的过程，好处是：

### 1 数组的解构赋值

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构 

```JavaScript
let [a, b, c] = [1, 2, 3];
```

上面代码表示，可以从数组中提取值，按照对应位置，对变量赋值。

- 如果解构不成功，变量的值就等于`undefined`。
- 另一种情况是**不完全解构**，即等号左边的模式，只匹配一部分的等号右边的数组。这种情况下，解构依然可以成功。 
- 如果等号的右边不是数组（或者严格地说，不是可遍历的结构，参见《Iterator》一章），那么将会报错。 
- ？对于 Set 结构，也可以使用数组的解构赋值。

```JavaScript
let [x, y, z] = new Set(['a', 'b', 'c']);
x // "a"
```

- ？事实上，只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。

```JavaScript
function* fibs() {
  let a = 0;
  let b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

let [first, second, third, fourth, fifth, sixth,seven,eight] = fibs();
first, second, third, fourth, fifth, sixth,seven,eight 
// 0 1 1 2 3 5 8 13
```

- ？上面代码中，`fibs`是一个 Generator 函数（参见《Generator 函数》一章），原生具有 Iterator 接口。解构赋值会依次从这个接口获取值。

**默认值**

```JavaScript
let [foo = true] = [];
foo // true
let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
let [x = 1] = [null];
x // null
```

【注意】ES6 内部使用严格相等运算符（`===`），判断一个位置是否有值。所以，只有当一个数组成员严格等于`undefined`，默认值才会生效；如果一个数组成员是`null`，默认值就不会生效，因为`null`不严格等于`undefined`。 

```javascript
function f() {
  console.log('aaa');
}
let [x = f()] = [1];
//上面代码中，因为x能取到值，所以函数f根本不会执行。上面的代码其实等价于下面的代码。
let x;
if ([1][0] === undefined) {
  x = f();
} else {
  x = [1][0];
}
```

### 2 对象的解构赋值

- 对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。

```JavaScript
let { bar, foo } = { foo: 'aaa', bar: 'bbb' };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: 'aaa', bar: 'bbb' };
baz // undefined，如果解构失败，值等于undefined
```

- 对象的解构赋值，可以很方便地将现有对象的方法，赋值到某个变量。

```JavaScript
// 例一：将Math对象的对数、正弦、余弦三个方法，赋值到对应的变量上
let { log, sin, cos } = Math;

// 例二：将console.log赋值到log变量
const { log } = console;
log('hello') // hello
```

- ❤ 【为非同名局部变量赋值】**对象的解构赋值的内部机制**，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。

```JavaScript
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"
foo // error: foo is not defined
//foo是匹配的模式，baz才是变量。
//即，读取名为foo的属性，并将其值存储在baz中
```

- 【嵌套对象解构】解构也可以用于嵌套结构的对象 

  在这个例子中，结构模式使用了花括号{}，其含义为找到node对象中loc属性后，应当深入一层继续查找start属性。所有冒号前的标识符都代表在对象中的索引位置，其右侧是被赋值的变量名；如果冒号后是花括号，意味着要赋的值在对象内部更深的层次中。

  ```JavaScript
  const node = {
    loc: {
      start: {
        line: 1,
        column: 5
      }
    }
  };
  
  let { loc, loc: { start }, loc: { start: { line }} } = node;
  line // 1
  loc  // Object {start: Object}
  start // Object {line: 1, column: 5}
  ```

- ? 对象的解构赋值可以取到继承的属性 

- ```javascript
  const obj1 = {};
  const obj2 = { foo: 'bar' };
  Object.setPrototypeOf(obj1, obj2);
  
  const { foo } = obj1;
  foo // "bar"
  //上面代码中，对象obj1的原型对象是obj2。foo属性不是obj1自身的属性，而是继承自obj2的属性，解构赋值可以取到这个属性。
  ```

**默认值**

- 默认值生效的条件是，对象的属性值严格等于`undefined`。 

  ```javascript
  var {x = 3} = {x: undefined};
  x // 3
  
  var {x = 3} = {x: null};
  x // null
  ```

  上面代码中，属性`x`等于`null`，因为`null`与`undefined`不严格相等，所以是个有效的赋值，导致默认值`3`不会生效。

- **【注意】**

  1. 已经声明的变量用于解构赋值，必须非常小心。 （一定要用一对小括号包裹解构赋值语句，js引擎将一对花括号视为一个代码块，语法规定，代码块语句不能出现在赋值语句左侧，添加小括号后可以将语句转化为一个表达式，从而实现整个解构赋值过程。）

     ```JavaScript
     // 错误的写法
     let x;
     {x} = {x: 1};
     // SyntaxError: syntax error
     //JavaScript 引擎会将{x}理解成一个代码块，从而发生语法错误。只有不将大括号写在行首，避免 JavaScript 将其解释为代码块，才能解决这个问题。
     // 正确的写法
     let x;
     ({x} = {x: 1});
     ```

  2. 解构赋值表达式（也就是=右侧表达式）如果为null或undefined会导致程序抛出错误。也就是说，任何尝试读取null或undefined的属性的行为都会触发运行错误

  3. 解构赋值允许等号左边的模式之中，不放置任何变量名。因此，可以写出非常古怪的赋值表达式。 

     ```javascript
     ({} = [true, false]);
     ({} = 'abc');
     ({} = []);//表达式虽然毫无意义，但是语法是合法的，可以执行
     ```

- 4. 由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。 

### 3 字符串的解构赋值

- 字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象。

```JavaScript
const [a, b, c, d, e] = 'hello';
a b c d e// "h" "e" "l" "l" "o"

let {length : len} = 'hello';
len // 5 类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值。
```

### 4 数值和布尔值的解构赋值

- 解构赋值时，如果等号右边是数值和布尔值，则会先转为对象。

```JavaScript
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
//上面代码中，数值和布尔值的包装对象都有toString属性，因此变量s都能取到值。
```

- 解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于`undefined`和`null`无法转为对象，所以对它们进行解构赋值，都会报错。

```JavaScript
let { prop: x } = undefined; // TypeError
let { prop: y } = null; // TypeError
```

### 5 函数参数的解构赋值

- 函数的参数也可以使用解构赋值 he 默认值

  ```javascript
  function move({x = 0, y = 0} = {}) {
    return [x, y];
  }
  move({x: 3, y: 8}); // [3, 8]
  move({x: 3}); // [3, 0]
  move({}); // [0, 0]
  move(); // [0, 0]
  //上面代码中，函数move的参数是一个对象，通过对这个对象进行解构，得到变量x和y的值。如果解构失败，x和y等于默认值。
  function move({x, y} = { x: 0, y: 0 }) {
    return [x, y];
  }
  move({x: 3, y: 8}); // [3, 8]
  move({x: 3}); // [3, undefined]
  move({}); // [undefined, undefined]
  move(); // [0, 0]
  //上面代码是为函数move的参数指定默认值，而不是为变量x和y指定默认值
  ```

  

### 6 圆括号问题

- ES6 的规则是，只要有可能导致解构的歧义，就不得使用圆括号。(建议只要有可能，就不要在模式中放置圆括号)

- 不能使用（）的情况

  1. 变量声明语句 ：let [(a)] = [1]; // 报错
  2. 函数参数 ：function f([(z)]) { return z; } //报错
  3. 赋值语句的模式 ：({ p: a }) = { p: 42 }; //报错

- 可以使用（）的情况

  1. 赋值语句的非模式部分，可以使用圆括号 

     ```JavaScript
     [(b)] = [3]; // 正确，模式是取数组的第一个成员，跟圆括号无关
     ({ p: (d) } = {}); // 正确，模式是p，而不是d
     [(parseInt.prop)] = [3]; // 正确，与第一行语句的性质一致
     //1、首先它们都是赋值语句，而不是声明语句；2、其次它们的圆括号都不属于模式的一部分
     ```

### 7 用途

**（1）交换变量的值**

```JavaScript
let x = 1;
let y = 2;
[x, y] = [y, x];
```

**（2）从函数返回多个值**

```JavaScript
// 返回一个数组
function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();

// 返回一个对象
function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```

**（3）函数参数的定义**

解构赋值可以方便地将一组参数与变量名对应起来。

```JavaScript
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);
// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```

**（4）提取 JSON 数据**

解构赋值对提取 JSON 对象中的数据，尤其有用。

```JavaScript
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};
let { id, status, data: number } = jsonData;
console.log(id, status, number);
// 42, "OK", [867, 5309]
```

**（5）? 函数参数的默认值**

```JavaScript
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
} = {}) {
  // ... do stuff
};
    //避免了在函数体内部再写var foo = config.foo || 'default foo';这样的语句
```

**（6）遍历 Map 结构**

任何部署了 Iterator 接口的对象，都可以用`for...of`循环遍历。Map 结构原生支持 Iterator 接口，配合变量的解构赋值，获取键名和键值就非常方便。

```JavaScript
const map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world
```

**（7）输入模块的指定方法**

加载模块时，往往需要指定输入哪些方法。解构赋值使得输入语句非常清晰。

```JavaScript
const { SourceMapConsumer, SourceNode } = require("source-map");
```



