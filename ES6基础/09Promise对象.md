[TOC]

## Promise对象

### 1 Promise 的含义(承诺)

所谓`Promise`，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。 

**特点：**

（1）对象的状态不受外界影响。`Promise`对象代表一个异步操作，有三种状态：`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。 

（2）一旦状态改变，就不会再变，任何时候都可以得到这个结果。`Promise`对象的状态改变，只有两种可能：从`pending`变为`fulfilled`和从`pending`变为`rejected`。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为 resolved（已定型）。 

`Promise`也有一些**缺点**。首先，无法取消`Promise`，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部。第三，当处于`pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。 

### 2 基本用法

`Promise`对象是一个构造函数，用来生成`Promise`实例。 

```JavaScript
const promise = new Promise(function(resolve, reject) {
  // ... some code
  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});//Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。它们是两个函数，由 JavaScript 引擎提供，不用自己部署。
```

**`resolve`函数**的作用是，将`Promise`对象的状态从“未完成”变为“成功”（即从 pending 变为 resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；**`reject`函数**的作用是，将`Promise`对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。 

`Promise`实例生成以后，可以用**`then`方法**分别指定`resolved`状态和`rejected`状态的回调函数。 

```JavaScript
promise.then(function(value) {
  // success
}, function(error) {//第二个函数可选
  // failure
});
//第一个回调函数是Promise对象的状态变为resolved时调用，第二个回调函数是Promise对象的状态变为rejected时调用。
//这两个函数都接受Promise对象传出的值作为参数。
```

下面是**异步加载图片**的例子。

```javascript
function loadImageAsync(url) {
  return new Promise(function(resolve, reject) {
    const image = new Image();
    image.onload = function() {	//加载成功
      resolve(image);
    };
    image.onerror = function() {	//加载失败
      reject(new Error('Could not load image at ' + url));
    };
    image.src = url;
  });
}//如果加载成功，就调用resolve方法，否则就调用reject方法。
```

下面是一个用`Promise`对象实现的 Ajax 操作的例子。

```javascript
const getJSON = function(url) {
  const promise = new Promise(function(resolve, reject){
    const handler = function() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    const client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();
  });
  return promise;
};

getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('出错了', error);
});
//getJSON是对 XMLHttpRequest 对象的封装，用于发出一个针对 JSON 数据的 HTTP 请求，并且返回一个Promise对象。
```



### 3 Promise.prototype.then()

### 4 Promise.prototype.catch()

### 5 Promise.prototype.finally()

### 6 Promise.all()

### 7 Promise.race()

### 8 Promise.resolve()

### 9 Promise.reject()

### 10 应用

### 11 Promise.try()









