[TOC]

## Iterator 和 for...of 循环

### 1 Iterator（遍历器）的概念

遍历器（Iterator）是一种接口，**为各种不同的数据结构提供统一的访问机制**。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。 

**作用：**

1. 为各种数据结构，提供一个统一的、简便的访问接口； 
2. 使得数据结构的成员能够按某种次序排列； 
3. ES6 创造了一种新的遍历命令`for...of`循环，Iterator 接口主要供`for...of`消费。 

**Iterator 的遍历过程 ：**

（1）创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。

（2）第一次调用指针对象的`next`方法，可以将指针指向数据结构的第一个成员。

（3）第二次调用指针对象的`next`方法，指针就指向数据结构的第二个成员。

（4）不断调用指针对象的`next`方法，直到它指向数据结构的结束位置。

**每一次调用`next`方法，都会返回数据结构的当前成员的信息。**就是返回一个**包含`value`和`done`两个属性的对象**。其中，`value`属性是当前成员的值，`done`属性是一个布尔值，表示遍历是否结束。 

下面是一个模拟`next`方法返回值的**例子**。 

```JavaScript
var it = makeIterator(['a', 'b']);

it.next() // { value: "a", done: false }
it.next() // { value: "b", done: false }
it.next() // { value: undefined, done: true }
//遍历器，done: false和value: undefined属性都是可以省略的
function makeIterator(array) {
  var nextIndex = 0;
  return {
    next: function() {
      return nextIndex < array.length ?
        {value: array[nextIndex++]} :
        {done: true};
    }
  };
}
```



### 2 默认 Iterator 接口

Iterator 接口的目的，就是为所有数据结构，提供了一种统一的访问机制，即`for...of`循环（详见下文）。 当使用`for...of`循环遍历某种数据结构时，该循环会自动去寻找 Iterator 接口。 

**一种数据结构只要部署了 Iterator 接口，我们就称这种数据结构是“可遍历的”（iterable）。** 



### 3 调用 Iterator 接口的场合

### 4 字符串的 Iterator 接口

### 5 Iterator 接口与 Generator 函数

### 6 遍历器对象的 return()，throw()

### 7 for...of 循环