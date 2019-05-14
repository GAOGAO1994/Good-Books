[TOC]

## Symbol

### 1 概述

ES6 引入了一种新的原始数据类型`Symbol`，**表示独一无二的值**。它是 JavaScript 语言的第七种数据类型，前六种是：`undefined`、`null`、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）。 

Symbol 值通过`Symbol`函数生成。这就是说，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的 Symbol 类型。 

```JavaScript
let s = Symbol();
typeof s
// "symbol"，Symbol函数前不能使用new命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象。
let s1 = Symbol('foo');
let s2 = Symbol('bar');
s1 // Symbol(foo)
s1.toString() // "Symbol(foo)"
//s1和s2是两个 Symbol 值。有了参数以后，就等于为它们加上了描述，输出的时候就能够分清，到底是哪一个值。
```

`Symbol`函数的参数只是表示对当前 Symbol 值的描述，因此相同参数的`Symbol`函数的返回值是不相等的。 

如果 Symbol 的参数是一个对象，就会调用该对象的`toString`方法，将其转为字符串，然后才生成一个 Symbol 值。 

Symbol 值不能与其他类型的值进行运算，会报错。 "your symbol is " + sym// TypeError: can't convert symbol to string

但是，Symbol 值可以显式转为字符串。 sym.toString() // 'Symbol(My symbol)'

另外，Symbol 值也可以转为布尔值，但是不能转为数值。 Boolean(sym) // true



### 2 Symbol.prototype.description

[ES2019](https://github.com/tc39/proposal-Symbol-description) 提供了一个实例属性`description`，直接返回 Symbol 的描述。 sym.description // "foo"

### 3 作为属性名的 Symbol

由于每一个 Symbol 值都是不相等的，这意味着 Symbol 值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性。 

```JavaScript
let mySymbol = Symbol();
// 第一种写法，Symbol 值作为对象属性名时，不能用点运算符。
let a = {};
a[mySymbol] = 'Hello!';
// 第二种写法，Symbol 值必须放在方括号之中。
let a = {
  [mySymbol]: 'Hello!'
};
// 第三种写法
let a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });
// 以上写法都得到同样结果
a[mySymbol] // "Hello!"
```

Symbol 类型还可以用于定义一组常量，保证这组常量的值都是不相等的。 

**Symbol 值作为属性名时，该属性还是公开属性，不是私有属性。** 

### 4 实例：消除魔术字符串

魔术字符串指的是，在代码之中多次出现、与代码形成强耦合的某一个具体的字符串或者数值。 

常用的消除魔术字符串的方法，就是把它写成一个变量。

```JavaScript
const shapeType = {
  triangle: 'Triangle' → triangle: Symbol()
};

function getArea(shape, options) {
  let area = 0;
  switch (shape) {
    case shapeType.triangle:
      area = .5 * options.width * options.height;
      break;
  }
  return area;
}

getArea(shapeType.triangle, { width: 100, height: 100 });
```

### 5 属性名的遍历

Symbol 作为属性名，该属性*不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回。*但是，它也不是私有属性，有一个`Object.getOwnPropertySymbols`方法，可以获取指定对象的所有 Symbol 属性名。 

```JavaScript
const objectSymbols = Object.getOwnPropertySymbols(obj);
objectSymbols
// [Symbol(a), Symbol(b)]
```

另一个新的 API，**`Reflect.ownKeys`方法可以返回所有类型的键名**，包括常规键名和 Symbol 键名。 



### 6 Symbol.for()，Symbol.keyFor()

希望重新使用同一个 Symbol 值，`Symbol.for`方法可以做到这一点。 

```JavaScript
let s1 = Symbol.for('foo');
let s2 = Symbol.for('foo');
s1 === s2 // true
```

`Symbol.for()`与`Symbol()`这两种写法，都会生成新的 Symbol。它们的区别是，**前者会被登记在全局环境中供搜索**，后者不会。`Symbol.for()`不会每次调用就返回一个新的 Symbol 类型的值，而是会先检查给定的`key`是否已经存在，如果不存在才会新建一个值。 

`Symbol.keyFor`方法返回一个已登记的 Symbol 类型值的`key`。 

`Symbol.for`为 Symbol 值登记的名字，是全局环境的，可以在不同的 iframe 或 service worker 中取到同一个值。 

### 7  ？实例：模块的 Singleton 模式

Singleton 模式指的是调用一个类，任何时候返回的都是同一个实例。 

### 8 内置的 Symbol 值

ES6 还提供了 11 个内置的 Symbol 值，指向语言内部使用的方法。 

**Symbol.hasInstance：** 指向一个内部方法。 当其他对象使用`instanceof`运算符，判断是否为该对象的实例时，会调用这个方法。比如，`foo instanceof Foo`在语言内部，实际调用的是`Foo[Symbol.hasInstance](foo)`。 

**Symbol.isConcatSpreadable：**`Symbol.isConcatSpreadable`属性等于一个布尔值，表示该对象用于`Array.prototype.concat()`时，是否可以展开。 

```JavaScript
let arr2 = ['c', 'd'];
arr2[Symbol.isConcatSpreadable] = false;
['a', 'b'].concat(arr2, 'e') // ['a', 'b', ['c','d'], 'e']
```

数组的默认行为是可以展开，`Symbol.isConcatSpreadable`**默认等于`undefined`**。该属性等于`true`时，也有展开的效果。 

类似数组的对象正好相反，默认不展开。它的`Symbol.isConcatSpreadable`属性设为`true`，才可以展开。 

```JavaScript
let obj = {length: 2, 0: 'c', 1: 'd'};
['a', 'b'].concat(obj, 'e') // ['a', 'b', obj, 'e']
```

**？Symbol.species：**指向一个构造函数。创建衍生对象时，会使用该属性。 

`Symbol.species`的作用在于，实例对象在运行过程中，需要再次调用自身的构造函数时，会调用该属性指定的构造函数。它主要的用途是，有些类库是在基类的基础上修改的，那么子类使用继承的方法时，作者可能希望返回基类的实例，而不是子类的实例。 

**？Symbol.match：**指向一个函数。当执行`str.match(myObject)`时，如果该属性存在，会调用它，返回该方法的返回值。 

**Symbol.replace：**指向一个方法，当该对象被`String.prototype.replace`方法调用时，会返回该方法的返回值。 

```JavaScript
String.prototype.replace(searchValue, replaceValue)
// 等同于
searchValue[Symbol.replace](this, replaceValue)
```

`Symbol.replace`方法会收到两个参数，第一个参数是`replace` 

**Symbol.search：**指向一个方法，当该对象被`String.prototype.search`方法调用时，会返回该方法的返回值。 

**Symbol.split：**属性，指向一个方法，当该对象被`String.prototype.split`方法调用时，会返回该方法的返回值。 

对象的**Symbol.iterator**属性，指向该对象的默认遍历器方法。 

对象的**Symbol.toPrimitive**属性，指向一个方法。该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。 

`Symbol.toPrimitive`被调用时，会接受一个字符串参数，表示当前运算的模式，一共有三种模式。

- Number：该场合需要转成数值
- String：该场合需要转成字符串
- Default：该场合可以转成数值，也可以转成字符串

对象的**Symbol.toStringTag**属性，指向一个方法。在该对象上面调用`Object.prototype.toString`方法时，如果这个属性存在，它的返回值会出现在`toString`方法返回的字符串之中，表示对象的类型。也就是说，这个属性可以用来定制`[object Object]`或`[object Array]`中`object`后面的那个字符串。 

ES6 新增内置对象的`Symbol.toStringTag`属性值如下。

- `JSON[Symbol.toStringTag]`：'JSON'
- `Math[Symbol.toStringTag]`：'Math'
- Module 对象`M[Symbol.toStringTag]`：'Module'
- `ArrayBuffer.prototype[Symbol.toStringTag]`：'ArrayBuffer'
- `DataView.prototype[Symbol.toStringTag]`：'DataView'
- `Map.prototype[Symbol.toStringTag]`：'Map'
- `Promise.prototype[Symbol.toStringTag]`：'Promise'
- `Set.prototype[Symbol.toStringTag]`：'Set'
- `%TypedArray%.prototype[Symbol.toStringTag]`：'Uint8Array'等
- `WeakMap.prototype[Symbol.toStringTag]`：'WeakMap'
- `WeakSet.prototype[Symbol.toStringTag]`：'WeakSet'
- `%MapIteratorPrototype%[Symbol.toStringTag]`：'Map Iterator'
- `%SetIteratorPrototype%[Symbol.toStringTag]`：'Set Iterator'
- `%StringIteratorPrototype%[Symbol.toStringTag]`：'String Iterator'
- `Symbol.prototype[Symbol.toStringTag]`：'Symbol'
- `Generator.prototype[Symbol.toStringTag]`：'Generator'
- `GeneratorFunction.prototype[Symbol.toStringTag]`：'GeneratorFunction'

对象的**Symbol.unscopables**属性，指向一个对象。该对象指定了使用`with`关键字时，哪些属性会被`with`环境排除。



## Set 和 Map 数据结构

### 1 Set

#### 基本用法

ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

`Set`本身是一个构造函数，用来生成 Set 数据结构。

```JavaScript
const s = new Set();
[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));
for (let i of s) {
  console.log(i);
}  // 2 3 5 4   Set 结构不会添加重复的值
const set = new Set([1, 2, 3, 4, 4]);
[...set]   // [1, 2, 3, 4]
console.log(set); //Set(4) {1, 2, 3, 4}
```

在 Set 内部，两个`NaN`是相等。另外，两个对象总是不相等的。

向 Set 加入值的时候，不会发生类型转换，所以`5`和`"5"`是两个不同的值。Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符（`===`），主要的区别是`NaN`等于自身，而精确相等运算符认为`NaN`不等于自身。 

**Set 实例的属性和方法：**

Set 结构的实例有以下属性。

- `Set.prototype.constructor`：构造函数，默认就是`Set`函数。
- `Set.prototype.size`：返回`Set`实例的成员总数。

Set 实例的方法分为两大类：**操作方法**（用于操作数据）和**遍历方法**（用于遍历成员）。下面先介绍四个操作方法。

- `add(value)`：添加某个值，返回 Set 结构本身。
- `delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
- `has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
- `clear()`：清除所有成员，没有返回值。

`Array.from`方法可以将 Set 结构转为数组。 

**遍历操作：**

Set 结构的实例有四个遍历方法，可以用于遍历成员。

- `keys()`：返回键名的遍历器

- `values()`：返回键值的遍历器

- `entries()`：返回键值对的遍历器

  ```JavaScript
  for (let item of set.entries()) {
    console.log(item);
  }
  // ["red", "red"]
  // ["green", "green"]
  // ["blue", "blue"]
  ```

- `forEach()`：使用回调函数遍历每个成员，`forEach`方法还可以有第二个参数，表示绑定处理函数内部的`this`对象 

  ```JavaScript
  let set = new Set([1, 4, 9]);
  set.forEach((value, key) => console.log(key + ' : ' + value))
  ```

`Set`的**遍历顺序就是插入顺序**。这个特性有时非常有用，比如使用 Set 保存一个回调函数列表，调用时就能保证按照添加顺序调用。 

**遍历的应用：**

扩展运算符（`...`）内部使用`for...of`循环，所以也可以用于 Set 结构。 

```JavaScript
let arr = [...set];  // ['red', 'green', 'blue']
```

使用 Set 可以很容易地实现**并集（Union）、交集（Intersect）和差集（Difference）**。

```JavaScript
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);
// 并集
let union = new Set([...a, ...b]);
// Set {1, 2, 3, 4}
// 交集
let intersect = new Set([...a].filter(x => b.has(x)));
// set {2, 3}
// 差集
let difference = new Set([...a].filter(x => !b.has(x)));
// Set {1}
```

```JavaScript
//还可以实现数组的去重
var arr = [1,3,2,4,5,6,8,6,4];
var newArr =[...new Set(arr.filter(item=> item>=6))]  ;
console.log(newArr);// [6, 8]
```

### 2 WeakSet

WeakSet 结构与 Set 类似，也是不重复的值的集合。 

两点区别：

1. WeakSet 的成员只能是对象，而不能是其他类型的值。 
2. WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中。 

> **垃圾回收机制**依赖引用计数，如果一个值的引用次数不为`0`，垃圾回收机制就不会释放这块内存。结束使用该值之后，有时会忘记取消引用，导致内存无法释放，进而可能会引发内存泄漏。WeakSet 里面的引用，都不计入垃圾回收机制，所以就不存在这个问题。因此，WeakSet 适合临时存放一组对象，以及存放跟对象绑定的信息。只要这些对象在外部消失，它在 WeakSet 里面的引用就会自动消失。 

WeakSet 的成员是不适合引用的，因为它会随时消失。 ES6 规定 WeakSet 不可遍历。 

**语法：**

```JavaScript
const ws = new WeakSet();   //WeakSet 是一个构造函数
```

WeakSet 可以接受一个数组或类似数组的对象作为参数。（实际上，任何具有 Iterable 接口的对象，都可以作为 WeakSet 的参数。） 

```JavaScript
const a = [[1, 2], [3, 4]];
const ws = new WeakSet(a);  // WeakSet {[1, 2], [3, 4]}
//是a数组的成员成为 WeakSet 的成员，而不是a数组本身。这意味着，数组的成员只能是对象。
const b = [3, 4];
const ws = new WeakSet(b);
// 数组b的成员不是对象，加入 WeaKSet 就会报错
```

WeakSet 结构有以下三个方法。

- **WeakSet.prototype.add(value)**：向 WeakSet 实例添加一个新成员。
- **WeakSet.prototype.delete(value)**：清除 WeakSet 实例的指定成员。
- **WeakSet.prototype.has(value)**：返回一个布尔值，表示某个值是否在 WeakSet 实例之中。
- WeakSet **没有`size`属性**，没有办法遍历它的成员。 

### 3 Map

JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是**传统上只能用字符串当作键**。这给它的使用带来了很大的限制。 

ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。 

```javascript
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);
```

注意，只有对同一个对象的引用，Map 结构才将其视为同一个键。这一点要非常小心。

```javascript
const map = new Map();
map.set(['a'], 555);
map.get(['a']) // undefined
```

如果 Map 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等，Map 将其视为一个键，比如`0`和`-0`就是一个键，布尔值`true`和字符串`true`则是两个不同的键。另外，`undefined`和`null`也是两个不同的键。虽然`NaN`不严格相等于自身，但 Map 将其视为同一个键。 

**实例的属性和操作方法：**

**（1）size 属性：**`size`属性返回 Map 结构的成员总数。

**（2）set(key, value)：**`set`方法设置键名`key`对应的键值为`value`，然后返回整个 Map 结构。如果`key`已经有值，则键值会被更新，否则就新生成该键。**可以采用链式写法。** 

**（3）get(key)：**`get`方法读取`key`对应的键值，如果找不到`key`，返回`undefined`。

**（4）has(key)：**`has`方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。

**（5）delete(key)：**`delete`方法删除某个键，返回`true`。如果删除失败，返回`false`。

**（6）clear()：**`clear`方法清除所有成员，没有返回值。

**遍历方法**

Map 结构原生提供三个遍历器生成函数和一个遍历方法。

- `keys()`：返回键名的遍历器。

- `values()`：返回键值的遍历器。

- `entries()`：返回所有成员的遍历器。

  ```JavaScript
  for (let item of map.entries()) {
    console.log(item[0], item[1]);
  }
  // "F" "no"
  // "T" "yes"
  ```

- `forEach()`：遍历 Map 的所有成员。

需要特别注意的是，Map 的遍历顺序就是插入顺序。

**与其他数据结构的互相转换：**

1. Map 结构转为数组结构，比较快速的方法是使用扩展运算符（`...`）。 

```JavaScript
[...map]  // [[1,'one'], [2, 'two'], [3, 'three']]
```

2.  **数组转Map：**将数组传入 Map 构造函数，就可以转为 Map。 

3. **Map转对象：**如果所有 Map 的键都是字符串，它可以无损地转为对象； 如果有非字符串的键名，那么这个键名会被转成字符串，再作为对象的键名。 

4. **对象转为Map：**

5. **Map转为JSON：**一种情况是，Map 的键名都是字符串，这时可以选择转为对象 JSON。；

   ```JavaScript
   function strMapToJson(strMap) {
     return JSON.stringify(strMapToObj(strMap));
   }
   let myMap = new Map().set('yes', true).set('no', false);
   strMapToJson(myMap)
   // '{"yes":true,"no":false}'
   ```

   另一种情况是，Map 的键名有非字符串，这时可以选择转为数组 JSON。 

   ```JavaScript
   function mapToArrayJson(map) {
     return JSON.stringify([...map]);
   }
   let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
   mapToArrayJson(myMap)
   // '[[true,7],[{"foo":3},["abc"]]]'
   ```

6. **JSON 转为 Map：** 正常情况下，所有键名都是字符串；有一种特殊情况，整个 JSON 就是一个数组，且每个数组成员本身，又是一个有两个成员的数组。这时，它可以一一对应地转为 Map。 

### 4 WeakMap

`WeakMap`与`Map`的区别有两点。

1. `WeakMap`只接受对象作为键名（`null`除外），不接受其他类型的值作为键名。
2. `WeakMap`的键名所指向的对象，不计入垃圾回收机制。 

它的键名所引用的对象都是弱引用，即垃圾回收机制不将该引用考虑在内。因此，只要所引用的对象的其他引用都被清除，垃圾回收机制就会释放该对象所占用的内存。也就是说，一旦不再需要，WeakMap 里面的键名对象和所对应的键值对会自动消失，不用手动删除引用。 

***典型应用场景是，在网页的 DOM 元素上添加数据，就可以使用`WeakMap`结构。当该 DOM 元素被清除，其所对应的`WeakMap`记录就会自动被移除。*** 

```JavaScript
const wm = new WeakMap();

const element = document.getElementById('example');

wm.set(element, 'some information');
wm.get(element) // "some information"
```

【注意】WeakMap 弱引用的只是键名，而不是键值。键值依然是正常引用。 

 **WeakMap 的语法**

WeakMap 与 Map 在 API 上的区别主要是两个

1. 一是没有遍历操作（即没有`keys()`、`values()`和`entries()`方法），也没有`size`属性。 
2. 二是无法清空，即不支持`clear`方法。 
3. `WeakMap`只有四个方法可用：`get()`、`set()`、`has()`、`delete()`。 

**WeakMap 的用途**

前文说过，WeakMap 应用的典型场合就是 DOM 节点作为键名。

1. 下面是一个例子。

```JavaScript
let myElement = document.getElementById('logo');
let myWeakmap = new WeakMap();

myWeakmap.set(myElement, {timesClicked: 0});

myElement.addEventListener('click', function() {
  let logoData = myWeakmap.get(myElement);
  logoData.timesClicked++;
}, false);
```

上面代码中，`myElement`是一个 DOM 节点，每当发生`click`事件，就更新一下状态。我们将这个状态作为键值放在 WeakMap 里，对应的键名就是`myElement`。一旦这个 DOM 节点删除，该状态就会自动消失，不存在内存泄漏风险。

2. 另一个用处是部署私有属性。 