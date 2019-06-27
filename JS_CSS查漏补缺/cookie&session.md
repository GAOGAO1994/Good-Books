[TOC]

# cookie & localStorage & sessionStorage

下面是我自己总结的常见前端存储方式的对比表格，我目前是这么理解的，如有问题，请大佬们指正：

![img](https://pic4.zhimg.com/80/v2-33fe4779709b66ab1b751d5a86371eab_hd.jpg)

## Cookie

> HTTP Cookie，通常直接叫做cookie，最初是在客户端用于存储会话信息的。该标准要求服务器对任意HTTP请求发送Set-Cookie HTTP头作为响应的一部分，其中包含会话信息。 ————————《JavaScript高级程序设计》

说的直白一点，一般就是用来登录验证，在发送登录请求的以后，服务端在返回的响应头中设置`Set-Cookie`，并设置其值。并且cookie遵循浏览器同源策略，在不同源的页面中是访问不到当前页面的cookie的。如果可以的，就能拿到cookie伪造请求进行XSS和CSRF攻击。

![img](https://pic1.zhimg.com/80/v2-ebd068580ba2545b8a43d82c01e764e8_hd.jpg)

就像上图所示，在响应头中有了`Set-Cookie`，并设置了服务端返回的相应的值，这个时候我们在查看当前页面的Application中的cookie，发现其中已经有了刚才返回的cookie。**会话cookie一般不会存储在硬盘里，而是保存在内存中，如果是设置了过期时间，浏览器会把cookie保存在硬盘中。**

![img](https://pic1.zhimg.com/80/v2-36ec08a1252ae847f15d245924e6b444_hd.jpg)

就上图中，我们可以看见返回的`Set-Cookie`中有时间，所以当前返回的cookie是保存在硬盘中的。**cookie会随着页面发送的请求再带回到服务端，然后服务端判断cookie的值，进而判断当前的登录状态。** 既然我们刚才设置的cookie是有有效时间的，所以在有效时间之前，我们不管怎么操作页面（关闭标签页或者浏览器），我们只要打开页面发送请求，就会默认我们已经是登录状态，不需要再次登录。

![img](https://pic1.zhimg.com/80/v2-d2b779dee8cd2ef850a247f91ef03d10_hd.jpg)

上图就是携带cookie的请求，这不是前端手动在请求中携带上的，是浏览器的自主行为。

### Ajax、Axios以及fetch携带cookie情况

刚才我们说了，只要服务端在请求回来的响应头中设置了`Set-Cookie`的值，服务端就在浏览器中种下了cookie，以后的每个请求都会携带上这个cookie，但是也有**个别情况发送的请求是不会默认携带cookie**。

**Cors跨域请求和fetch请求默认是不携带cookie的**

### jQuery中的Ajax请求

我们不管jQuery中发送的jsonp请求，因为严格意义上讲，jsonp不算是发送请求后端返回数据的形势。我们现在只讨论发送的json请求。在相同域下，我们发送的Ajax请求⬇️

```
$.ajax({
  type: 'post',
  url: '/person/detail',
  dataType: 'json',
  data: {
    id: 1
  },
  success: function (res) {},
  error: function(e) {}
})
```

如果我们请求的是一个不同域下的接口，我们不考虑反向代理的情况，因为反向代理理论上还是访问相同域的接口。下面是如果我们使用Cors解决跨域的时候⬇️

```
$.ajax({
  type: 'post',
  url: '/person/detail',
  dataType: 'json',
  data: {
    id: 1
  },
  xhrFields: {
    withCredentials: true // 如果是Cors解决的跨域，我们请求接口的域和我们页面所在域是不同域，所以需要添加这个属性值
  },
  success: function (res) {},
  error: function(e) {}
})
```

### Axios请求

下面是我们在vue或者react库中经常用到一个请求库——Axios，Axios发送Cors跨域请求，默认也是不会带上cookie的⬇️

```
axios({
  method: 'post',
  url: '/person/detail',
  data: {
    id: 1,
  }
});
```

上面是正常的同域请求，下面我们来看怎么发送Cors请求

```
axios({
  method: 'post',
  url: '/person/detail',
  data: {
    id: 1,
  },
  withCredentials: true, // 设置了这个值，我们我就可以发送Cors请求了，这个值默认不设置的话是false
});
```

### fetch请求

fetch是javascript提供的一个比较底层的API，让我们可以方便的发起fetch请求，但是fetch请求现在看来，只是一个底层API，虽然相对于原生Ajax请求方便一些，但是Ajax已经被各种库封装的很方便使用了，可fetch就相形见绌了。就我们现在来看，fetch不仅仅是在发送Cors请求的时候不懈怠cookie，而是默认情况下，fetch什么情况都不会从服务端发送或接收任何cookied，我们先来看正常请求⬇️

```
fetch('/person/detail', {
  method: 'POST',
  body: JSON.stringify({id: 1}),
  headers: {
    'content-type': 'application/json'
  },
})
```

上面我们就发送了一条fetch请求，然后我们需要请求凭证，需要有cookie怎么办呢⬇️

```
fetch('/person/detail', {
  method: 'POST',
  body: JSON.stringify({id: 1}),
  credentials: 'include', // 强制带上凭据头，携带上cookie
  headers: {
    'content-type': 'application/json'
  },
})
```

### 操作cookie

我们毕竟这章是讲前端的数据存储，上面说的请求携带cookie的方法，只是因为**cookie会跟随请求被携带回服务端** ，那我们现在来看怎么操作cookie呢，或者拿到cookie的值，但是我们一般不需要这么做。

### 存储的数据格式

> 下面是一个cookie的构成，摘自《JavaScript高级程序设计》

1. **名称：** 一个唯一确定 cookie 的名称。cookie 名称是不区分大小写的，所以 myCookie 和 MyCookie 被认为是同一个 cookie。然而，实践中最好将 cookie 名称看作是区分大小写的，因为某些服务器会这样处理 cookie。cookie 的名称必须是经过 URL 编码的。
2. **值：** 储存在 cookie 中的字符串值。值必须被 URL 编码。
3. **域：** cookie 对于哪个域是有效的。所有向该域发送的请求中都会包含这个cookie信息。这个值可以包含子域（subdomain，如 [http://www.wrox.com](https://link.zhihu.com/?target=http%3A//www.wrox.com)），也可以不包含它（如.[http://wrox.com](https://link.zhihu.com/?target=http%3A//wrox.com)，则对于 [http://wrox.com](https://link.zhihu.com/?target=http%3A//wrox.com)的所有子域都有效）。如果没有明确设定，那么这个域会被认作来自设置 cookie 的那个域。
4. **路径：** 对于指定域中的那个路径，应该向服务器发送 cookie。例如，你可以指定 cookie 只有从[http://www.wrox.com/books/](https://link.zhihu.com/?target=http%3A//www.wrox.com/books/) 中才能访问，那么 [http://www.wrox.com](https://link.zhihu.com/?target=http%3A//www.wrox.com) 的页面就不会发送 cookie 信息，即使请求都是来自同一个域的。
5. **失效时间：** 表示 cookie 何时应该被删除的时间戳(也就是，何时应该停止向服务器发送这个cookie)。默认情况下，浏览器会话结束时即将所有 cookie 删除;不过也可以自己设置删除时间。 这个值是个 GMT 格式的日期(Wdy, DD-Mon-YYYY HH:MM:SS GMT)，用于指定应该删除 cookie 的准确时间。因此，cookie可在浏览器关闭后依然保存在用户的机器上。如果你设置的失 效日期是个以前的时间，则 cookie 会被立刻删除。
6. **安全标志：** 指定后，cookie 只有在使用 SSL 连接的时候才发送到服务器。例如，cookie 信息只 能发送给[https://www.wrox.com](https://link.zhihu.com/?target=https%3A//www.wrox.com)，而 [http://www.wrox.com](https://link.zhihu.com/?target=http%3A//www.wrox.com) 的请求则不能发送 cookie。每一段信息都作为 Set-Cookie 头的一部分，使用分号加空格分隔每一段，如下例所示。

### 获取cookie，更改cookie

我们通过js是很方便获取cookie的只需要`document.cookie`，我们就能获取到当前页面的cookie。

![img](https://pic4.zhimg.com/80/v2-9023f752841b760dc2e023a4250183af_hd.jpg)

上面在控制台通过`document.cookie`获取到的cookie

![img](https://pic1.zhimg.com/80/v2-610b1ddb11b3eb5a669746749b33fd40_hd.jpg)

我们发现我们获取到的cookie是一长串字符串，而我们在Application看到是已经经过序列化的cookie了。我们既然获取到了cookie，剩下的就是自己进行序列化处理，操作字符串了，我就不多赘述了。

我们更改cookie的话，如果有相同值，会覆盖，如果没有，会创建新的

![img](https://pic2.zhimg.com/80/v2-ac4b23e098644a0015fb8bd981f64ef1_hd.jpg)

通过上面我们能知道`document.cookie`是如何获取和更改cookie值的，不管cookie保存了什么，都会跟随请求一并带到服务端去。如果我们需要频繁操作cookie的值，我们可以自己封装操作cookie的get、set方法。

------

cookie就这些，毕竟我们现在应该不会大量使用cookie去完成前端数据存储的工作，一是存储的数量太小，二是请求都会带上，浪费带宽。也因为请求会带上cookie，所以中间穿插了一些请求携带cookie的点。

------

## Web Storage

Web Storage 克服了由cookie代来的一些限制，当数据需要被严格控制在客户端上时，无须持续地将数据发回服务器。Web Storage的两个主要目的：
**1. 提供一种在cookie之外的存储会话数据的途径；**
**2. 提供一种存储大量可以跨会话存在的数据的机制。**

### sessionStorage和localStorage共同的Storage Api

sessionStorage和localStorage同属于Web Storage，虽然他们的有效时间不同，但是有着相同的方法供开发者使用

```JavaScript 
// clear方法，可以删除sessionStorage中所有的值
sessionStorage.clear(); // 清除所有sessionStorage中的数据
localStorage.clear(); // 清除所有localStorage中的数据

// getItem(name) 根据指定的名字name获取对应的值
var name = sessionStorage.getItem('name'); // 获取key为name的value
var name = localStorage.getItem('name'); // 获取key为name的value
// 当然，我们也可以不这么着获取storage中的值，也可以像下面这样获取值
var name = sessionStorage.name; // 获取key为name的value
var name = localStorage.name; // 获取key为name的value

// key(index) 可以获取到index位置处的值的名字
var key = sessionStorage.key(0); // 获取到了sessionStorage中排在第一个的值的key，比如刚才的’name‘
var key = localStorage.key(0); // 获取到了localStorage中排在第一个的值的key，比如刚才的’name‘
// 获取到了排在第一位的key值，我们就可以根据这个key获取对应的value了
var value = sessionStorage.getItem(key); // 这样我们就获取到了排在第一位的name的value值
var value = localStorage.getItem(key); // 这样我们就获取到了排在第一位的name的value值

// removedItem(name) 删除由name指定的键值对
sessionStorage.removedItem('name'); // 删除了key为name的value
localStorage.removedItem('name'); // 删除了key为name的value
// 我们也能使用删除对象中属性的delete方法来删除
delete sessionStorage.name;
delete localStorage.name;

// setItem(name, value) 为指定的name设置一个对应的值
sessionStorage.setItem('name', 'zhanwuzha'); // 在sessionStorage中存了一个name，值为zhanwuzha
localStorage.setItem('name', 'zhanwuzha'); // 在localStorage中存了一个name，值为zhanwuzha
// 我们也可以使用另一种方法来设置
sessionStorage.name = 'zhanwuzha'; // 在sessionStorage中存了一个name，值为zhanwuzha
localStorage.name = 'zhanwuzha'; // 在localStorage中存了一个name，值为zhanwuzha
```

以上就是Web Storage中的方法，涵盖了增删改查。`delete`操作符在WebKit中无法删除数据，所以我们还是使用`removeItem()`方法吧；

Web Storage中保存的数据，不会跟随请求一同发回服务器，这是跟cookie最大的区别，并且是按键值对来保存数据的，虽然cookie也是键值对，但是是类似`'name=zhanwuzha&age=16'`这样的字符串，还需要在进一步处理。

### sessionStorage

> sessionStorage对象存储特定于某个会话的数据，也就是该数据只保持到浏览器关闭。这个对象就像会话cookie，也会在浏览器关闭后消失。存储在sessionStorage中的数据可以跨越页面的刷新而存在，同时如果浏览器支持，浏览器崩溃并重启之后依然可用（Firefox和WebKit都支持，IE则不行）。——————《JavaScript高级程序设计》

sessionStorage在满足上面storage共同的方法之外，它的使用是绑定在某个会话的：

1. 遵循浏览器的同源策略，不同源的根本就访问不到
2. 符合同源策略的，必须是同一个会话即使是相同地址，不是同一个会话也不行

例子：

![img](https://pic2.zhimg.com/80/v2-b8052ae46921215143f9ab23248b8c4d_hd.jpg)

我们在页面中设置一个sessionStorage，key为`name`，value为`zhanwuzha`,这个时候我们点击页面中的“新页面打开相同页”的按钮在新标签页打开页面

![img](https://pic2.zhimg.com/80/v2-b35eb5651aa89b8292d02075a00649a9_hd.jpg)

我们打开新标签页的sessionStorage中依然有我们刚才保存的数据，可是如果我们直接在地址栏输入地址呢

![img](https://pic1.zhimg.com/80/v2-e07420ef9edf4d6d8d6fabeeed24d3cc_hd.jpg)

这个时候就没有了，所以我们发现只有处在相同会话的sessionStorage；
**通过点击链接（或者用了 window.open）打开的新标签页之间是属于同一个 session的，但新开一个标签页总是会初始化一个新的 session，即使网站是一样的，它们也不属于同一个 session**

这下小伙伴知道什么情况下页面会共享同一个sessionStorage了吧

### localStorage

相比sessionStorage，localStorage就容易理解的多，只要通过上面提到的方法存储的数据，不手动进行删除的情况下，会一直保留，并且只要符合同源策略的页面，都是可以共享相同的localStorage。

**就这么好理解~**

## indexedDB

### what is indexedDB

IndexedDB 就是浏览器提供的本地数据库，它可以被网页脚本创建和操作。我们都知道cookie只能存储4到5kb的数据，而Web Storage则可以存储2.5mb到10mb之间（各家浏览器不同），而indexedDB一般来说不少于250mb，甚至没有上限。感觉就是个很牛逼的技术。

不仅拥有大量的存储空间，还支持建立索引，异步，事务等真正数据库已有的功能

下面有阮一峰大神些的专门聊indexedDB的博客，大神写的很详细，有概念，有操作，感觉以后可能真能用的上这样的技术，所以可以提前了解一下

## 废弃

**文中没有提及到的Web SQL和globalStorage基本处于废弃状态，所以就没有写啦**

## 参考

1. [《浏览器数据库 IndexedDB 入门教程》——阮一峰](https://link.zhihu.com/?target=http%3A//www.ruanyifeng.com/blog/2018/07/indexeddb.html)
2. [《sessionStorage 的数据会在同一网站的多个标签页之间共享吗？这取决于标签页如何打开》](https://link.zhihu.com/?target=https%3A//github.com/lmk123/blog/issues/66)

 