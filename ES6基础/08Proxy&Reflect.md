[TOC]

# Proxy & Reflect

## Proxy（代理器）

### 1 概述

Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程。 **可以对外界的访问进行过滤和改写** 

ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例。

```JavaScript
var proxy = new Proxy(target, handler);
//target参数表示所要拦截的目标对象
//handler参数也是一个对象，用来定制拦截行为。
```

Proxy 对象的所有用法，都是上面这种形式，不同的只是`handler`参数的写法。其中，`new Proxy()`表示生成一个`Proxy`实例。

例子：

```JavaScript
var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});
proxy.time // 35
proxy.name // 35
proxy.title // 35
```



### 2 Proxy 实例的方法

下面是 Proxy 支持的拦截操作一览，一共 13 种。

- **get(target, propKey, receiver)**：拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`。

  1. 接受三个参数，依次为目标对象、属性名和 proxy 实例本身（可选） 

- **set(target, propKey, value, receiver)**：拦截对象属性的设置，比如`proxy.foo = v`或`proxy['foo'] = v`，返回一个布尔值。

  1. 接受四个参数，依次为目标对象、属性名、属性值和 Proxy 实例本身，其中最后一个参数可选 

- **has(target, propKey)**：拦截`propKey in proxy`的操作，返回一个布尔值。

- 1. 接受两个参数，分别是目标对象、需查询的属性名。 

- **deleteProperty(target, propKey)**：拦截`delete proxy[propKey]`的操作，返回一个布尔值。如果这个方法抛出错误或者返回`false`，当前属性就无法被`delete`命令删除。 

- **ownKeys(target)**：拦截`Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in`循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys()`的返回结果仅包括目标对象自身的可遍历属性。

  拦截以下操作。

  - `Object.getOwnPropertyNames()`
  - `Object.getOwnPropertySymbols()`
  - `Object.keys()`
  - `for...in`循环

  **注意，**使用`Object.keys`方法时，有三类属性会被`ownKeys`方法自动过滤，不会返回。

  - 目标对象上不存在的属性
  - 属性名为 Symbol 值
  - 不可遍历（`enumerable`）的属性

- **getOwnPropertyDescriptor(target, propKey)**：拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。

- **defineProperty(target, propKey, propDesc)**：拦截`Object.defineProperty(proxy, propKey, propDesc）`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。

- **preventExtensions(target)**：拦截`Object.preventExtensions(proxy)`，返回一个布尔值。

- **getPrototypeOf(target)**：拦截`Object.getPrototypeOf(proxy)`，返回一个对象。

  拦截下面这些操作。

  - `Object.prototype.__proto__`
  - `Object.prototype.isPrototypeOf()`
  - `Object.getPrototypeOf()`
  - `Reflect.getPrototypeOf()`
  - `instanceof`

- **isExtensible(target)**：拦截`Object.isExtensible(proxy)`，返回一个布尔值。

- **setPrototypeOf(target, proto)**：拦截`Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。

- **apply(target, object, args)**：拦截 Proxy 实例作为函数调用的操作，比如`proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。

- 1. 接受三个参数，分别是目标对象、目标对象的上下文对象（`this`）和目标对象的参数数组。 

- **construct(target, args)**：拦截 Proxy 实例作为构造函数调用的操作，比如`new proxy(...args)`。

  - `target`：目标对象
  - `args`：构造函数的参数对象
  - `newTarget`：创造实例对象时，`new`命令作用的构造函数



### 3 Proxy.revocable()

`Proxy.revocable`方法返回一个可取消的 Proxy 实例。 

```JavaScript
let target = {};
let handler = {};

let {proxy, revoke} = Proxy.revocable(target, handler);

proxy.foo = 123;
proxy.foo // 123

revoke();
proxy.foo // TypeError: Revoked
```

`Proxy.revocable`的一个使用场景是，目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问。 

### 4 this 问题

在 Proxy 代理的情况下，目标对象内部的`this`关键字会指向 Proxy 代理。 



### 5 实例：Web 服务的客户端

Proxy 对象可以拦截目标对象的任意属性，这使得它很合适用来写 Web 服务的客户端。 

```JavaScript
const service = createWebService('http://example.com/data');

service.employees().then(json => {
  const employees = JSON.parse(json);
  // ···
});
//上面代码新建了一个 Web 服务的接口，这个接口返回各种数据。Proxy 可以拦截这个对象的任意属性，所以不用为每一种数据写一个适配方法，只要写一个 Proxy 拦截就可以了。
```



## Reflect

### 1 概述

**设计目的：**

（1） 将`Object`对象的一些明显属于语言内部的方法（比如`Object.defineProperty`），放到`Reflect`对象上。 

（2） 修改某些`Object`方法的返回结果，让其变得更合理。比如，`Object.defineProperty(obj, name, desc)`在无法定义属性时，会抛出一个错误，而`Reflect.defineProperty(obj, name, desc)`则会返回`false`。 







### 2 静态方法

### 3 实例：使用 Proxy 实现观察者模式

观察者模式（Observer mode）指的是函数自动观察数据对象，一旦对象有变化，函数就会自动执行。 

```JavaScript
const person = observable({
  name: '张三',
  age: 20
});

function print() {
  console.log(`${person.name}, ${person.age}`)
}

observe(print);
person.name = '李四';
// 输出
// 李四, 20
```

使用 Proxy 写一个观察者模式的最简单实现，即实现`observable`和`observe`这两个函数。思路是`observable`函数返回一个原始对象的 Proxy 代理，拦截赋值操作，触发充当观察者的各个函数。 

```JavaScript
const queuedObservers = new Set();

const observe = fn => queuedObservers.add(fn);
const observable = obj => new Proxy(obj, {set});

function set(target, key, value, receiver) {
  const result = Reflect.set(target, key, value, receiver);
  queuedObservers.forEach(observer => observer());
  return result;
}
```

