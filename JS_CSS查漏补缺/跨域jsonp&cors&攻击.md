[TOC]

# [跨域](https://segmentfault.com/a/1190000015597029)

（参考文章，以及相关好文综合）

## 1 浏览器的同源策略

> **同源策略**限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互。这是一个用于隔离潜在恶意文件的重要安全机制。————————《MDN web docs》

上面这段话说的直白一点就是，我在一个域名地址下的网页，如果请求一个受到同源策略限制的地址接口的时候，就会报错，这是为了在我的网页下请求别的地址的文件可能是恶意代码，造成不必要的问题。默认从自己同一个源请求的文件就是。。。安全可信赖的。

### 什么样是同源，什么样是非同源

那按照同源策略，不同源的是不能进行请求等操作的，那什么样的是同源，什么样的又不是同源呢（我想从事前端的小伙伴多多少少应该都应该知道）；
只要满足 **协议、主机、端口** 一致，则两个页面具有相同的源

------

例子：假如我们从`http://www.zhanwuzha.com/home/index.html`向以下地址发送请求
1. `http://www.zhanwuzha.com/home/detail.html` 成功，路径不同
2. `http://www.zhanwuzha.com/description/detail.html` 成功，路径不同
3. `https://www.zhanwuzha.com/home/list.html` 失败，协议不同（http 和 https）
4. `http://www.zhanwuzha.com:8848/home/manange.html` 失败， 端口不同（默认80 和 8848）

5. `http://mobile.zhanwuzha.com/home/secret.html` 失败，域名不同（www 和 mobile）

**IE例外：**涉及到同源策略时，Internet Explorer 有两个主要的不同点

- **授信范围**（Trust Zones）：两个相互之间高度互信的域名，如公司域名（corporate domains），不遵守同源策略的限制。
- **端口**：IE 未将端口号加入到同源策略的组成部分之中，因此 `http://company.com:81/index.html` 和 `http://company.com/index.html`  属于同源并且不受任何限制。

### CSRF攻击&DOM查询

> **为什么要有同源策略：**“同源策略限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互。这是一个用于隔离潜在恶意文件的重要安全机制。” 
>
> **没有同源的两大危害：**浏览器是从两个方面去做这个同源策略的，一是针对接口的请求，二是针对Dom的查询。 
>
> 1. **CSRF攻击** 
>
>    **cookie**大家应该知道，一般用来处理登录等场景，目的是让服务端知道谁发出的这次请求。<u>如果你请求了接口进行登录，服务端验证通过后会在响应头加入Set-Cookie字段，然后下次再发请求的时候，浏览器会自动将cookie附加在HTTP请求的头字段Cookie中，服务端就能知道这个用户已经登录过了 。</u>
>
>    你饶有兴致地浏览着www.nidongde.com，谁知这个网站暗地里做了些不可描述的事情！由于没有同源策略的限制，它向www.maimaimai.com发起了请求！聪明的你一定想到上面的话“服务端验证通过后会在响应头加入Set-Cookie字段，然后下次再发请求的时候，浏览器会自动将cookie附加在HTTP请求的头字段Cookie中”，这样一来，这个不法网站就相当于登录了你的账号，可以为所欲为了！ 
>
> 2. **没有同源策略限制的Dom查询**
>
>    平时访问的银行网站是www.yinhang.com，而现在访问的是www.yinghang.com，这个钓鱼网站做了什么呢？
>
>    ```JavaScript 
>    // HTML
>    <iframe name="yinhang" src="www.yinhang.com"></iframe>
>    // JS
>    // 由于没有同源策略的限制，钓鱼网站可以直接拿到别的网站的Dom
>    const iframe = window.frames['yinhang']
>    const node = iframe.document.getElementById('你输入账号密码的Input')
>    console.log(`拿到了这个${node}，我还拿不到你刚刚输入的账号密码吗`)
>    ```
>
>    ### 

## 2 跨域的正确打开方式

**总结一句话：跨域是不可能靠前端单方面解决的，不管是怎么解决，都需要服务端的支持** 

## JSONP

我相信只要从事过前端的小伙伴用没用过不说，但应该都听说过`JSONP`这个技术。毕竟面试问不问，准备的时候都会了解。那`JSONP`到底是怎么解决跨域的呢？

### `src`和`href`属性

刚才说了只要**协议、主机、端口**不一致，就会有跨域的问题，但是，HTML的标签中有一个属性是可以请求外部地址的，那就是`src`和`href`属性⬇️

```JavaScript 
<img src="http://www.zhanwuzha.com/media/001.jpeg"></script>
<link rel="stylesheet" href="http://www.zhanwuzha.com/css/reset.css"> 

<script src="https://cdn.bootcss.com/jquery/3.4.0/jquery.min.js"></script>
<link href="https://cdn.bootcss.com/twitter-bootstrap/4.3.1/css/bootstrap.min.css" rel="stylesheet">
```

以上这种引用，我们肯定都知道是可以的，可以请求外部地址的js或者css，请求cdn服务器上的公共资源，并且不会出现问题，所以根据`src`的这一个特性，优秀的工程师们想到一个解决跨域的办法，俗称`JSONP`。

### JSON with Padding

下面我们就来看看`JSONP`到底是怎么实现的跨域请求
大家都知道`<script>`标签不管是从哪请求回来的js文件，都会立即执行，好，那我们看下面的代码⬇️
index.html

```HTML 
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>JSONP</title>
  <script src="http://www.zhanwuzha.com/jsonp/js/index.js"></script>
</head>
<body>
</body>
</html>
```

jsonp/js/index.js

```
console.log('我是src请求回来的js文件，我被执行了')
```

不出意外，我们浏览器的控制台肯定会输出‘我是src请求回来的js文件，我被执行了’这句话，那我们再来看下面的示例 index.html

```HTML 
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>JSONP</title>
  <script>
    function print(str) {
      console.log(str)
    }
  </script>
  <script src="http://www.zhanwuzha.com/jsonp/js/index.js"></script>
</head>
<body>

</body>
</html>
```

jsonp/js/index.js

```JavaScript 
print('我是src请求回来的js文件，我被执行了')
```

不出意外的话，这个也会输出‘我是src请求回来的js文件，我被执行了’这句话，但是跟刚才不同，我们是实现声明好了一个叫`print`的方法，然后在`jsonp/js/index.js`返回的js文件中，调用这个方法，然后顺利的执行，那我们是不是可以通过动态添加`<script>`的标签，可控的去请求远端js并执行，接下来我们来看一个完整版的请求⬇️
index.html

```HTML 
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>JSONP</title>
  <script>
    function print(data) {
      console.log(`我叫${data.name}`);
      console.log(`我今年${data.age}岁`);
      let jsonpScript = document.getElementsByClassName('jsonpScript')[0];
      document.body.removeChild(jsonpScript);
    }
    function jsonpRequest(callback) {
      let jsonpScript = document.createElement('script');
      jsonpScript.src = `http://www.zhanwuzha.com/jsonp/js/index.js?callback=${callback}`;
      jsonpScript.className = 'jsonpScript';
      document.body.appendChild(jsonpScript);
    }
  </script>
</head>
<body>
  <button onclick="jsonpRequest('print')">发个JSONP请求</button>
</body>
</html>
```

jsonp/js/index.js

```JavaScript 
print({
    name: '前端战五渣',
    age: 18
  })
```

这样我们就实现了点击页面上的按钮，我们去执行`jsonpRequest()`方法，并且传进去参数`"print"`的字符串作为回调函数的名字，显示在页面上添加一个`<script>`标签并且src的地址为`http://www.zhanwuzha.com/jsonp/js/index.js?callback=print`，等js文件请求回来以后执行我们先前传入的名为`"print"`的方法，并且传入了一个对象作为参数，这时控制台就会输出两句话‘我叫前端战五渣\n 我今年18岁’。
**后端拿到了callback=print只需要进行字符串拼接，调用名为print的方法，并且把需要传回来的数据作为参数拼进去，jsonp/js/index.js就是后端动态生成的**
这样我们就完整的进行了一次`JSONP`的请求
**其实JSONP并不算真正意义上的AJAX请求，只是请求了一个js文件并且执行了，而且这种跨域方法只能进行GET请求**

## CORS

接下来我们要讲得这个CORS方法的全称是Cross-origin resource sharing，中文名叫“跨域资源共享”，这可能是目前很多公司解决跨域问题的方法，它允许浏览器向跨源服务器，发出`XMLHttpRequest`请求，从而克服了AJAX只能同源使用的限制。

**这一节我们能知道其实同源策略不仅仅是浏览器这边做了限制，在服务端也是有限制的**

### 简单请求

只要满足以下条件的请求，就属于简单请求
\1. 请求方法为HEAD、GET或者POST中的一种 2. HTTP的头信息不超过以下几种字段`Accept`、`Accept-Language`、`Content-Language`、`Last-Event-ID`以及`Content-Type`的值只限于`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`三个

对于简单请求来说，从浏览器发出请求的时候，浏览器会自动在请求头中添加一个字段`Origin`，值为发出请求网页的源地址

![img](https://pic3.zhimg.com/80/v2-a30a1883fcbf07f8c7d86e9b73511186_hd.jpg)

服务端根据这个值，决定是否同意这次请求，如果`Origin`的值不在指定的许可范围，服务端返回一个正常的HTTP回应。浏览器发现，这个回应的头信息没有包含Access-Control-Allow-Origin字段，就知道出错了，从而抛出一个错误，被XMLHttpRequest的onerror回调函数捕获。注意，这种错误无法通过状态码识别，因为HTTP回应的状态码有可能是200。 如果Access-Control-Allow-Origin字段正好跟带过去的`Origin`的值一样，则返回对应的数据，完成一次请求。

### 非简单请求以及`option`请求

非简单请求是那种对服务器有特殊要求的请求，比如请求方法是`PUT`或`DELETE`，或者`Content-Type`字段的类型是`application/json`。

在进行非简单请求之前，浏览器会在正式请求之前发送一次预检请求，这就是有时候我们会在控制台中看到的`option`请求，就是说，正式请求之前，浏览器会去问服务端我这个地址能不能访问你，如果可以，浏览器才会发送正式的请求，否则报错。

------

**关于简单请求和非简单请求的相关字段以及解释，可以看阮一峰大佬的博客，虽然是2016年写的，但是通俗易懂不过时《跨域资源共享 CORS 详解》**

------

### CORS总结

总的来说，CORS实现跨域的方法就是根据请求头的`Origin`值和响应头的`Access-Control-Request-Headers`和`Access-Control-Request-Method`的值进行比对，通过了就可以请求成功，没通过就请求失败。

## 反向代理

### node反向代理

如果我们用的是node起的前端服务，那我们可以使用node来直接进行反向代理

![img](https://pic2.zhimg.com/80/v2-d59cbaa673a28e66144cb0aecdde155d_hd.jpg)

以上是我用express框架起的服务，我们只需要引入一个处理代理的插件，然后如图中的配置，代理接口地址，然后配置一个`target`，就是需要代理到的地址，起服务，监听`8848`端口；这时我们假如我们的ip地址是`http://127.0.0.1:8848`，我们就可以访问当我们的页面，然后从页面中向`http://127.0.0.1:8848/a`以及`http://127.0.0.1:8848/b`接口发送请求的时候，前端服务接到请求会向`http://127.0.0.1:8080/a`以及`http://127.0.0.1:8080/b`的接口请求，然后再返回数据。这就用反向代理实现了跨域请求，因为我们的前端服务在`8848`端口，而要请求的端口在`8080`，所以实现了一次完美的跨域请求。

**不仅用node可以反向代理，有一大部分的公司用的是nginx进行的代理（nginx没怎么玩过，就不献丑了），或者进行CORS来解决跨域，所以解决的方案一样，只是工具的选择不同**

### 为什么反向代理可以跨域

如上面所说，我们在前端服务器上代理了后端的接口，我们只需要访问跟页面在同一源地址下的接口就行了，但是这是为什么呢？

一开始我们就说过，**同源策略只是浏览器的一个安全策略**，只适用于浏览器向服务器发送请求的时候，当服务器跟服务器发送请求的时候，自然就没有这么一层限制，只要是接口，就会返回。

有人说那不需要权限验证吗？？当然需要了，但是在权限不通过的时候，也是会请求成功200的，只是返回的是“需要登录”等信息，但是如果是浏览器跨域访问这个接口，返回的只能是一个错误。

### 反向代理和正向代理

什么是正向代理和反向代理，我先来一张图

![img](https://pic2.zhimg.com/80/v2-d9aa7396972483022e3146b18f8d2bdd_hd.jpg)

### 正向代理

什么是正向代理呢，众所周知的科学上网，就是一个很典型的实例，

![img](https://pic1.zhimg.com/80/v2-0950c6f393d08fb09c02552588255fe8_hd.jpg)

当我们从客户端没有办法访问到目标服务器的时候，我们可以通过正向代理，把请求代理到一个有权访问到目标服务器的代理服务器，然后让代理服务器去请求目标服务器，拿到数据再返回来，进而实现科学上网的目的。在整个过程中，我们用户，客户端其实是很清楚实际数据是哪台服务器返回来的，很明显是目标服务器返回的数据，只是中间经过了别人一手而已。

### 反向代理

反向代理的例子其实不用再说了，就是我们将node时候的作用，在上面的反向代理的图中，我们可以看到，是各个客户端都会向代理服务器发送请求，然后代理服务器再向各个真正获取数据的服务器发送请求获取数据。相对正向代理，反向代理的客户端，是不清楚数据到底是从哪台服务器获取的，他们只知道是从代理服务器返回的数据，在反向代理的流程中，其实在客户端的眼里代理服务器就是目标服务器，并不知道存在真正的目标服务器

### 总结

**到底是正向代理还是反向代理，只需要判断客户端知不知道真正返回数据的服务器在谁，知道就是正向代理，不知道就是反向代理**

------

**以上就是我工作到现在对解决跨域的理解，当然还有别的方法解决跨域，我说的是我接触到的比较常见的解决方案，在介绍CORS解决方案的时候不是很详细，因为我觉得阮一峰大佬说的很清楚，我就不赘述了。其他的如果有问题，各位大佬可以call我，在这向各位大佬递头了**

------

## 参考资料

1. [《MDN web docs》](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)
2. [《跨域资源共享 CORS 详解》](https://link.zhihu.com/?target=http%3A//www.ruanyifeng.com/blog/2016/04/cors.html)
3. [《jsonp原理详解——终于搞清楚jsonp是啥了》](https://link.zhihu.com/?target=https%3A//blog.csdn.net/hansexploration/article/details/80314948)

 





