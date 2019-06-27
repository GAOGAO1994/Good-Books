[TOC]

# node基础

## :star::star::star::star::star:nodejs特点

1. 单线程

- 大多数的服务器采用多线程机制，即每进来一个用户就创建一个线程，每个线程大约耗费2M内存，一个8G内存的服务器大约可同时支持4000个用户
- node采用单线程的机制，即同一个线程处理多个用户的操作，通过非阻塞io和事件驱动的机制，宏观上达到了并行的效果。一个8G内存的服务器大概能接受4万个用户。
- 单线程的好处是，不再有创建和销毁线程的开销；缺点是，一个用户导致线程奔溃，整个线程都会奔溃。

2. 非阻塞io

- 传统的多线程操作，当处理数据库时，整个线程会停止，等到数据库结果返回后在去执行后续操作，这就是阻塞，降低了效率。
- node采用非阻塞io的机制，当事件进入到数据库操作时，不等待，直接执行之后的操作，将数据库处理结果放到回调函数中，返回到事件队列中等待执行。
- io执行完毕时，将以事件形式通知执行io操作的线程，线程执行他的回调函数，线程必须有**事件循环**，不断去检测有没有未处理的事件。所以CPU利用率100%，一个线程永远在做计算

3. 事件驱动

- 在node中，用户提交数据或点击操作都会触发相应的事件，触发的事件放到回调函数中，同一时刻，node的线程只能执行一个回调函数，但是中途可以处理另一个回调函数（比如另一个用户出发了操作）执行完后返回之前的回调函数继续执行，这就成为“事件环”
- node是c++写的，v8也是，底层代码大部分都是对事件队列、回调函数队列的构建
- node中所有io都是异步的，回调函数嵌套着回调函数

4. **nodejs没有web容器**

### node适用场景

- 善于io任务调度，但不善于需要大量CPU计算的情况
- 与webscoket结合，开发长连接的实时交互应用
- 用户表单收集、聊天室、提供json的API

## 1 命令行工具

CDM（命令行，shell，DOS...都是指命令行）

常用指令

- dir：列出当前目录下的所有文件
- cd Desktop：跟目录名，进入指定的目录
- .：当前目录
- ..：上一级目录
- md xxxx：新建文件夹
- rd xxx：删除一个文件夹
- node hello.js：执行js文件

环境变量：Windows系统变量

- path环境变量：当我们在命令行窗口打开一个文件或程序，首先会在当前文件夹下寻找，如果找到则直接打开，如果没找到，则依次到环境变量的path路径中寻找，没有就报错。**类似于作用域链**
- 所以讲经常需要访问的程序和文件路径添加到path中（搭环境时需要配置）

## 2 进程和线程

**进程**

- 进程负责为程序运行提供必备**环境**
- 进程相当于工厂的车间

**线程**

- 计算机中的最小计算单位
- 负责执行进程中的程序
- 相当于工厂工人

单线程：一个人干所有活(js、浏览器)，js执行时，css执行会停滞

多线程：多个人干活（主流使用）

## 3 nodejs简介

能在服务端运行的js运行环境

让js不止在浏览器运行，还可以在服务器（系统中）运行，可以直接操作系统。

node采用chrome V8引擎运行js代码，使用事件驱动

客户端 —（请求/响应）— 服务器 —（i/o:input output）— 数据库

每个步骤都提速，可以提高性能

我们可以做的：

- 响应速度
- i/o速度（硬件问题）：磁盘速度有限制，所有项目的瓶颈到最后都是io，io操作会阻塞线程
- node模式：单线程，（传统都是来一个请求，创建一个线程）不会产生io阻塞。
- node好在单线程，对服务器要求比较低，同样的需求，node对硬件要求更低，奇数版开发版，偶数为稳定版

## 4 node的用途

访问量太大的还是有限制，还是得用java

淘宝：客户端和服务器中间加一个node服务器，专门渲染页面



## 5 commenjs规范

ECMAScript标准的缺陷

- js没有模块性，但有模块化
- 标准库较少
- 没有标准接口
- 缺乏管理系统

**模块化：**把完整的程序分成一个个的模块，把其中可重复使用的模块单独分出来，实现复用，方便升级

### **commonjs规范**

- 为弥补当前JavaScript没有标准的缺陷
- 为js指定美好愿景，希望js可以在任何地方运行
- 定义简单：模块引用、模块定义、模块标识（一个js就是一个模块）

### **模块引入及导出：**

- 在node中，通过require()函数来引入外部模块，传入文件路径，将自动引入外部文件

  require("./xxx.js")，必须 . 或 .. 开头，如果有，直接使用，没有向上寻找，如果磁盘根目录都没有就报错

- require引入模块后，该函数会返回一个对象，这个对象代表引入的模块。

  在node中，每一个js文件中的js代码都是独立运行在一个函数中的，而不是全局作用域，所以一个模块中的变量和函数在其他模块无法访问，所以要将内容暴露出去

- 向外部暴露属性或方法：exports

  只要将需要暴露的变量或方法设置为exports属性即可

  exports.x = "xxx";

### **模块分类**

- 核心模块：由node引擎提供的模块

  引入时直接使用：var xx = require("xx")，不需要 提供路径

- 文件模块：由用户自己创建的模块

  文件模块的标识就是文件的路径（绝对路径，相对路径）

- 当node执行模块中的代码时，他会首先在代码的顶部添加如下代码

  ```JavaScript 
  function (exports, require, module, __filename, __dirname) { }
  ```

  实际上模块中的代码都是包装在一个函数中执行的，并且在函数执行时，同时传入了5个实参

  - exports：该对象用来将变量或函数暴露到外部

  - require：函数，用来引入模块

  - module：代表当前模块本身；exports是module的属性：module.exports === exports，都可以使用

    但是当需要同时导出多个属性方法时，需要使用

    ```javascript
    module.exports = {
        name:'',
        sayName:...
    }
    // exports只能通过.的方式向外暴露内部变量
        //module.exports可以.也可以直接赋值
    ```

  - __filename：当前模块的完整路径

  - __dirname：当前文件文件夹路径

### **global**

- 在node中有一个全局对象global，它的使用和网页中的window类似
- 在全局中创建的变量都会作为global的属性保存
- 在全局中创建的函数都会作为global的方法保存
- 当前模块中var的变量是函数中的局部变量，可以用arguments.callee来验证

### **package：增强版的模块**

- commontJs的包规范允许我们将一组相关的模块组合到一起，形成一组完整的工具。

- commonJS的包规范由 包结构 和 包描述文件 两部分组成

- 包结构：用于组织包中的各种文件

  包实际上是一个压缩文件，解压后还原为目录，符合规范的目录，应该包含：**- package.json 描述文件，说明书** - bin 可执行二进制文件 - lib js代码 - doc 文档 - test 单元测试

- 包描述文件package.json：描述包的相关信息，以供外部读取分析

  在根目录下，一打开就能看见

- - dependencies：依赖 - 以来的其他包
  - devDependencies：开发依赖，生产时不需要
  - name，version，keywords，maintainers，licenses，。。。
  - :warning:json文件中不能有注释！！！

### **npm （Node Package Manager）**

- CommonJs包规范是理论，npm是实践

- 对于node而言，npm帮助其完成了第三方模块的发布、安装和依赖等。借助npm，node与第三方模块之间形成了很好的一个生态系统

- 安装完node后，可以直接使用

- 必须有package.json文件，才可以操作npm install

- npm init ：将会带领你创建package.json

- 再输入npm install可以创建node_modules

- 命令：

- - npm version：查看所有模块版本

  - npm search 包名  ：搜做包

  - npm install / i 包名 ：安装包

  - npm remove 包名：删除包

  - npm install 包名 --save ：安装包并添加到依赖dependencies

  - npm install：下载当前依赖中的包

  - npm install 包名 -g：全局安装的包，一般都是一些工具，比如css编译等

  - npm下载的包都放到mode_modules里，直接通过包名引入就可以

    var xxx = require("xxx")

## **buffer（缓冲区）**

- Buffer的解构和数组相似，操作的方法也和数组类似

- 数组中不能存储二进制文件，而buffer专门用来存储二进制数据

- 使用buffer不需要引入模块，直接使用即可

- **Buffer.from(buf)：**将一个字符串转换为buffer

  ```JavaScript 
  var str = 'hello world';
  var buf = Buffer.from(str);
  console.log(buf);
  // <Buffer 68 65 6c 6c 6f 20 77 6f 72 6c 64>
  // buffer中存储的数据都是二进制数据，但是显示时都是16进制形式，Unicode编码，
  ```

  **因为数据在传输时，都是电信号，01格式的，buffer每个元素范围都是00-ff （0-255）**

  计算机中，一个0 或一个1 称为1位（或1bit）

  8bit = 1byte（字节），1024byte = 1kb

- buf.length ：占用内存的大小

- 创建一个指定大小的buffer：

  var buf = new Buffer(10)；10字节的buffer**buffer的所有构造函数不推荐使用**

  **var buf = Buffer.alioc(10)；**buf[0] = 255 // 十进制自动转为16进制

- :warning:buffer的大小一旦确定，不可更改。因为buffer对应底层的一个空间（硬要加空间不连续，影响性能）

- 如果输入数值超过255，那么会取后8位的二进制数

- 只要数字在控制台输出，都会转换为10进制格式，否则用toString(2)转换进制

- **Buffer.allocUnsafe(size)**：创建一个指定大小的buffer，但是buffer中可能含有敏感数据

- **Buffer.toString()：**将缓冲区的数据转为字符串



## http模块

nodejs中，将很多的功能，划分为了一个个module

```JavaScript 
var http = require('http');
http.createServer(function (req,res) {
    console.log(req.url);
    res.writeHead(200,{"Content-Type":"text/plain;charset=UTF8"})
    res.end('好得');
}).listen(3000,'127.0.0.1');
```

**:star:request**

- 最关键的是req.url属性，表示用户的请求URL地址
- 所有的路由设计都是通过req.url来实现的
- 我们比较关心的不是拿到URL，而是识别这个URL









## fs（file system文件系统）

**文件系统简单来说就是通过node来操作系统中的文件**

node中，与文件系统的交互是非常重要的，服务器的本质

- 使用文件系统需要先引入fs模块：fs是核心模块，直接引入不需要下

  ```JavaScript 
  var fs = require("fs");
  ```

- **同步和异步调用：**fs中所有操作都有两种形式可供选择：同步（--Sync）、异步（--）

  同步文件会阻塞程序的执行，除非操作完成，不会向下执行代码

  异步文件系统不会阻塞程序的执行，通过回调函数将结果返回

- **同步文件的写入：**

- - 手动操作：新建文件 — 打开文件 — 向文件中写入 — 保存

    1. 打开文件：**fs.openSync(path, flags[, mode])**

  - - - `path`:要打开的文件的路径

    - - `flags` ：打开文件要做的类型：r(只读)、w（可写）、a（追加写入）

    - - `mode` ：设置文件操作权限，一般不传，**默认值:** `0o666` 

      返回值：var fd = fs.openSync(path, flags[, mode])  该方法node xx.js后，返回一个文件的描述符作为结果，可以通过描述符对文件进行各种操作

- - 2. 写内容：**fs.writeSync(fd, string[, position[, encoding]])**

       - `fd`：文件的描述符， 打开文件时的返回值

       - `string`：传入的内容，可以使用文本字符串

       - `position`

       - `encoding`

       - 返回: 写入的字节数。

         ```JavaScript 
         fs.writeSync(fd,"console.log('ok')");
         ```

- - 3. 关闭文件：**fs.closeSync(fd)**

       为了节省内存，一定要关闭

    ```JavaScript 
     var fd=fs.openSync('run.js','w');
    console.log(fd);
    
     fs.writeSync(fd,`
     var a = 'ok perfect';
    console.log(a);
     `);
     fs.closeSync(fd);
    ```

- **异步文件的写入**

- 1. 打开文件：**fs.open(path, flags[, mode], callback)**

     :heavy_exclamation_mark:异步调用的方法，结果都是通过回调函数的参数返回

     `callback` <function>

     - `err` ：回调函数的第一个参数，错误对象
     - `fd` ：回调函数的第二个参数，文件的操作符

- 2. 写入文件：**fs.write(fd, string[, position[, encoding\]], callback)

     :warning:在打开文件的回调函数中写！！！！

     `callback`

     - `err` 
     - `written`
     - `string` 

  3. 关闭：**fs.close(fd, callback)**

     :warning:在打开文件的回调函数最后写

     `callback`

     - `err` 

  ```JavaScript 
  fs.open('run.js','w',function (err,fd) {
      // console.log(arguments);
      if (!err){
          // console.log(fd);
          fs.write(fd,"console.log('text!!!')",function (err,written,string) {
              if (!err){
                  console.log('write ok');
              }
              //关闭文件
              fs.close(fd,function (err) {
                  if(!err){
                      console.log('close ok')
                  }
              });
          });
      }else {
          console.log(err);
      }
  })
  ```

- **简单文件写入：** **fs.writeFile(file, data[, options], callback)**

  **打开到写入到关闭，一步完成**

  - `file`文件名或文件描述符。
  - `data` 要写入的数据
  - `options` 选项，可以对写入进行一些设置，一般省略不写
    - `encoding`**默认值:** `'utf8'`。（页码）
    - `mode`**默认值:** `0o666`。
    - `flag` 参阅[支持的文件系统标志](http://nodejs.cn/s/JjbY8n)。**默认值:** `'w'`。
  - `callback` 当写入完成后执行的操作
    - `err`

> ⚠️⚠️⭐️'w',写入文件的内容，一般会全部重写，云本内容消失
>
> ​		不想删除时，可以用"a"

- **流式文件写入：** 文件不是一次性传输，

  同步、异步、简单文件的写入，性能较差，容易导致内存溢出

  - 流式文件写入：**fs.createWriteStream(path[, options])**

  - 1. 创建一个可写流，只要流存在，可以一直写入

       - `path`:<string> | <buffer>，文件路径

       - `options`：配置参数

         - `flags` 参阅[支持的文件系统标志](http://nodejs.cn/s/JjbY8n)。**默认值:** `'w'`。
         - `encoding` **默认值:** `'utf8'`。
         - `fd`**默认值:** `null`。
         - `mode`**默认值:** `0o666`。
         - `autoClose` **默认值:** `true`。
         - `start` 

       - 返回:<fs.WriteStream>参阅[可写流](http://nodejs.cn/s/9JUnJ8)。

         ```JavaScript 
         var ws = fs.createWriteStream("run.js");
         
         ws.write('this is stream content \n');
         ws.write('this is stream content \n');
         ws.write('this is stream content \n');
         ```

    2. 可以通过监听流的open和close事件来监听流的打开和关闭

       - on(事件字符串，回调函数)：可以为对象绑定一个事件
       - once(事件字符串，回调函数)：可以为对象绑定一个一次性的事件，该事件将会在触发一次后自动失效

       ```JavaScript 
       var ws = fs.createWriteStream("run.js");
       ws.once('open',function () {
           console.log('stream opened');
       });
       ws.once('close',function () {
           console.log('stream closed');
       });
       
       ws.write('this is stream content \n');
       ws.write('this is stream content2 \n');
       ws.write('this is stream content3 \n');
       
       // ws.end(); // 在发送端关闭流
       ws.close();// 关闭接收端流
       ```

- **同步文件读取：** **fs.readFileSync(path[, options])**

- **异步文件读取：** **fs.readFile(path[, options], callback)**

  - 返回的数据是buffer的16进制数据，需要用toString()转为字符串格式，返回buffer是因为，返回的不一定是文本内容，可能是图片

- **简单文件读取： ** 

- 

- **流式文件读取：** 

  如果要读取一个可读流中的数据，必须要为可读流绑定一个data事件，data事件绑定完毕，他会自动开始读取数据

  - 直接将可读流写到可写流中：wr.pipe(ws)



### 监听文件的变化： fs.watchFile(filename[, options\], listener)





## 试着做一个表单提交，模拟服务器







## 复习：

nodejs开发服务器，数据、路由。本地关心的效果，交互

nodejs实际上是极客开发出的一个小玩具，有着别人不具备的怪异特点

单线程、非阻塞io、事件驱动。

nodejs和别人不一样：

1. 没有自己的语法，使用V8引擎，V8引擎就是解析js的，效率非常高，并且V8中很多都是异步的，node就是将V8中的一些功能自己没有重写（别人做了，自己就站在巨人的肩膀上），移植到node中



系统中80端口是http默认的端口



## 模块

每个JavaScript都是一个模块，文件之间相互require完成一整个功能，一整个功能又是一个宏观的模块

js中的变量、方法、对象等只在文件内有效，要想在另一个文件中引用，需要暴露exports，一个文件可以暴露无数个变量，但是只需要一个require来接收，用点语法进行调用。

可以用module.exports = Xx来暴露一个类，Xx为构造函数，可以在引入文件中new来创建一个实例对象使用

模块就是功能的封装，常用的功能有人封装成模块，放到社区中供大家下载使用，这个社区就是npm（node package management）。

### 路径

如果引入路径不写'./'默认 node_module 中引入文件，就算不在啊当前目录下，也可以运行，会逐层向外寻找

如果require(xxx)没有后缀，会默认引用n_m对应文件夹中的入口文件（index.js），这个入口文件一般在对应的package.json中定义，main定义的

## 模板引擎（过时）

**ejs**

**jade模板引擎**：靠缩进来表示标签。。。很牛逼



## express框架

node开发，会有很多问题：

- 呈现静态页面很不方便，需要处理每个http请求，要考虑304 的问题
- 路由处理不直观，需要写很多正则表达式和字符串函数
- 不能集中精力写业务，要考虑很多其他东西

express框架解决了上述问题，非常想jQuery框架，改变了书写的习惯

- express的哲学相当于：**在你的想法和服务器之间充当薄薄的一层**，尽量少的干预你，让你充分表达自己的思想，提供有用的东西

**安装**

- npm install express

### 路由

1. 正则表达式可以被使用，未知部分用圆括号来分组，然后可以用req.params[0]、[1]得到
2. 可以用:xx来获取，用params.xx得到，params["xx"]得到



- 用get访问直接使用：app.get("网址",(req,res)=>{})

- - 所有get参数，？后面的都已经被忽略，锚点#也被忽略

- - 路由到/a，实际/a?id=2&sex=nan也能被处理

- post请求：app.post("网址",(req,res)=>{})

- 想处理这个网站的任何method请求，那么写all

  ​	app.all("/",()=>{})

这里的网址不分大小写

- 适合进行restful路由设计。简单说，就是一个路径，但是http method不同，对这个页面的使用不同
- - get：读取信息
  - add：添加信息
  - delete：删除信息

### 中间件

如果我们的get、post回调函数中，没有next参数，那么就匹配上第一个路由

不会往下匹配了，如果想往下匹配，需要写next()

```JavaScript 
app.get("/",(req,res,next)=>{
   res.render("form");
   next();
});
app.get("/",(req,res)=>{
    res.render("form");
});
```

- 解决1：交换位置，也就是说，express中所有的路由（中间件）的顺序至关重要。匹配上第一个，就不会往下匹配了。写路由表的时候要把抽象的往下写，具体的往上写。
- 解决2：加next()但是会重复执行，报错，此时就检索数据库，如果不存在再next()、
- 路由post、get这些东西就是中间件，中间件讲究顺序，匹配上第一个之后，就不会往后匹配了。next()函数能够继续往后匹配

app.use([path,] callback [, callback...])：use是一种特殊的中间件，网址后面随便写。给了我们增加多个功能的便利场所。

```JavaScript 
app.use('/admin',function (req,res) {
   res.send('hello');
});
// /admin/aaxxx都可以访问，可以往下无限扩展
```



当你不写路径的时候，相当于 "/" ，相当于所有网址

###  render

大多数时候，渲染内容用res.render()，将会根据views中的模板文件进行渲染，如果不想使用views文件夹，想自己设置文件夹名字，那么可以

app.set("views","xxx")



send（）只能用一次，end（）

### 处理GET、POST请求

- GET请求的参数在URL中，在原生node中，需要使用URL模块来识别参数字符串。在express中，不需要使用URL模块了，直接使用req.query对象
- POST请求在express中不能直接获得，必须使用bode.parser模块。使用后，将可以用req.body得到参数。但是如果表单中含有文件上传，那么还是需要使用formidable模块。

