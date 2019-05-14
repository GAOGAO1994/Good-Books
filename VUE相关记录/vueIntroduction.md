[TOC]

# VUE introduction

## 1 introduction

### 1.1 前端框架与库的区别

- jQuery：DOM（操作DOM）+请求
- art-template库：模板引擎
- 框架：全方位功能齐全（简易的DOM+发请求+模板引擎+路由功能）
- KFC的世界历史，库是啊小套餐，框架是全家桶
- 代码上的不同
  - 一般使用库的代码，是调用某个函数，我们自己把控库的代码
  - 框架：初始化自身的一些行为：执行编写的代码；
  - vue主要操作数据，来控制视图

Vue.js 被定义成一个用来开发 Web 界面的**前端库**，Vue.js 本身具有响应式编程和组件化的特点

所谓**响应式编程** , 即为**保持状态和视图的同步**，（MVC/MVVM/ MVW）框架都对这一特性进行了实现（也 称之为**数据绑定**），但这几者的实现方式和使用方式都不相同。

Vue.js 声明实例 **new Vue({ data : data })** 后自然对 data 里面 的数据进行了视图上的绑定。修改 data 的数据，视图中对应数据也会随之更改。 

Vue.js 的**组件化**理念和 ReactJS 异曲同工——“一切都是组件”，可以将任意封装好的代码注册成标签，例如：**Vue.component('example', Example)**，可以在模板中以 <example></ example>的形式调用。很大程度上能减少重复开发，而且配合 Vue.js的**周边工具vue-loader**，我们*可以将一个组件的CSS、HTML和js都写在一个文件里， 做到**模块化的开发***。 

如**vue-router和vue-resource**， 支持了**路由**和**异步请求**，这样就满足了开发单页面应用的基本条件。

**SPA**：是 `single page web application` 的缩写。中文翻译为 **单页应用程序** 或 **单页Web应用**。

[详细解说](https://blog.csdn.net/fungleo/article/details/77575077)

> 有的前端人员都应该明白我们的页面的 url 构成：
>
> http://www.fengcms.com/index.html?name=fungleo&old=32#mylove/is/world/peace
> 如上的 url 由以下部分组成：
>
> - http:// 规定了页面采用的协议。
>
> - www.fengcms.com 为页面所属的域名。
>
> - index.html 为读取的文件名称。
>
> - ?name=fungleo&old=32 给页面通过 GET 方式传送的参数
>
> - mylove/is/world/peace 为页面的**锚点区域**
>
>   前面四个发生改变的时候，会触发浏览器的跳转亦或是刷新行为，而更改 `url` 中的锚点，并不会出现这种行为，因此，**几乎所有的 `spa` 应用都是利用锚点的这个特性来实现路由的转换。**

这里，我们说的 vue 不仅仅是 vue.js 这一个文件，而是围绕 vue.js 配套的一系列的工具。

> 具体如下：
>
> vue.js 核心，不解释。
> VueRouter2 实现路由组织工具。
> webpack 项目打包以及编译工具。
> nodejs 前端开发环境。
> npm 前端包管理器。
> axios ajax 接口请求工具。
> sass-loader 和 node-sass css 预处理。
> element 基于 vue 的后台组件库。
> iview 基于 vue 的另一套后台组件库。
> vue-cli vue 项目脚手架。一键安装 vue 全家桶的工具。



## 2 创建vue项目

cmd命令行工具，下载安装相关脚手架工具后，创建项目

1. cd打开要创建的目录下
2. vue init webpack vue-demo-cnodejs
3. 接下的步骤按着提示走

### 2.1 初始文件解析

├── README.md                       // 项目说明文档
├── node_modules                    // 项目依赖包文件夹
├── build                           // 编译配置文件，一般不用管
│   ├── build.js
│   ├── check-versions.js
│   ├── dev-client.js
│   ├── dev-server.js
│   ├── utils.js
│   ├── vue-loader.conf.js
│   ├── webpack.base.conf.js
│   ├── webpack.dev.conf.js
│   └── webpack.prod.conf.js
├── config                          // 项目基本设置文件夹
│   ├── dev.env.js              // 开发配置文件
│   ├── index.js                    // 配置主文件
│   └── prod.env.js             // 编译配置文件
├── index.html                      // 项目入口文件
├── package-lock.json           // npm5 新增文件，优化性能
├── package.json                    // 项目依赖包配置文件
├── src                             // 我们的项目的源码编写文件
│   ├── App.vue                 // APP入口文件
│   ├── assets                      // 初始项目资源目录，回头删掉
│   │   └── logo.png
│   ├── components              // 组件目录
│   │   └── Hello.vue           // 测试组件，回头删除
│   ├── main.js                 // 主配置文件
│   └── router                      // 路由配置文件夹
│       └── index.js            // 路由配置文件

└── static                          // 资源放置目录

### 2.2 配置 src 目录

我们的这个项目是要做两个页面，*一个是 cnodejs 的**列表页面**，一个是**详情页面***。

所以，我把项目文件夹整理成如下的结构

├── App.vue                         // APP入口文件
├── api                             // 接口调用工具文件夹
│   └── index.js                    // 接口调用工具
├── components                      // 组件文件夹，目前为空
├── config                          // 项目配置文件夹
│   └── index.js                    // 项目配置文件，可以配置跨域的问题
├── frame                           // 子路由文件夹
│   └── frame.vue                   // 默认子路由文件
├── main.js                         // 项目配置文件
├── page                                // 我们的页面组件文件夹
│   ├── content.vue             // 准备些 cnodejs 的内容页面
│   └── index.vue                   // 准备些 cnodejs 的列表页面
├── router                          // 路由配置文件夹
│   └── index.js                    // 路由配置文件
├── style                           // scss 样式存放目录
│   ├── base                        // 基础样式存放目录
│   │   ├── _base.scss          // 基础样式文件
│   │   ├── _color.scss     // 项目颜色配置变量文件
│   │   ├── _mixin.scss     // scss 混入文件
│   │   └── _reset.scss     // 浏览器初始化文件
│   ├── scss                        // 页面样式文件夹
│   │   ├── _content.scss       // 内容页面样式文件
│   │   └── _index.scss     // 列表样式文件
│   └── style.scss              // 主样式文件
└── utils                           // 常用工具文件夹
    └── index.js                    // 常用工具文件
因为我们删除了一些默认的文件，所以这个时候项目一定是报错的，先不管他，我们根据我们的需求，新建如上的项目结构。这些都是在 src 目录里面的结构。

### 2.3 配置 static 目录

这个目录比较简单，因为这个项目我们的资源不多，但是，为了我的这系列博文能够适合大多数的项目的开发，一般，我搞成下面这个样子：

├── css             // 放一些第三方的样式文件
├── font                // 放字体图标文件
├── image           // 放图片文件，如果是复杂项目，可以在这里面再分门别类
└── js              // 放一些第三方的JS文件，如 jquery

> 你可能很奇怪，我们不是把样式和 JS 都写到里面去么，为什么还要在这边放呢？
>
> *（因为，如果是放在 src 目录里面，则每次打包的时候，都需要打包的。这回增加我们的打包项目的时间长度。而且，一些地方放的文件，我们一般是不会去修改的，也没必要 npm 安装，直接引用就好了。你可以根据自己的情况，对这些可以不进行打包而直接引用的文件提炼出来，放在资源目录里面直接调用，这样会大大的提高我们的项目的打包效率。）*

## 3 vue基础理解

1. 引包

2. 启动new VUE（{el:目的地，template:模板内容}）

3. options

   - 目的地el

   - 内容template

   - 数据data保存数据属性

     ```JavaScript
     data:{
        msg:' '
     }
     //是一个对象
     data:function(){
         return:{
             msg:''
         }
     }
     //组件化必须用返回匿名函数
     ```

     

   - **数据驱动视图**，关注视图层

4. 插值表达式  {{表达式}}

   - 对象不要连续3个{{ {name:'jack'} }}
   - 字符串{{ 'xxx' }}
   - 判断后的布尔值{{ true }}
     - 三元表达式{{ true?'正确':'错误' }}只能用于简单的判断
   - 可以用于页面中简单粗暴的调试
   - 注意：必须在

5. 什么是指令

   - 在vue中提供了一些对于页面+数据的更方便的输出，这些操作就叫做指令，以**v-xxx**表示
   - 如在angular中用

### 3.1 单向数据绑定：v-bind

![1555562133841](C:\Users\user\AppData\Roaming\Typora\typora-user-images\1555562133841.png)

![1555562240306](C:\Users\user\AppData\Roaming\Typora\typora-user-images\1555562240306.png)

单向数据绑定：v-bind，**可以缩写成——:class=""**

defineProperty的属性详解

### 3.2 双向数据绑定：v-model（语法糖）

**v-model == v-bind + v-on**

v-on可以缩写成——@click=""

v-model有局限，只适用于有value的属性（表单控件）

```JavaScript
//只有表单控件才可以实现双向数据绑定
new Vue({
        el:'#app',
        //只有表单控件才可以实现双向数据绑定
        template:'<div>' +
                //语法糖
                    '<input type="text" v-model="msg">' +
                '{{msg}}'+
                '</div>',
        data:function () {
            return{
                msg:'双向绑定'
            }
        }
    })
```

![1555568812894](C:\Users\user\AppData\Roaming\Typora\typora-user-images\1555568812894.png)

**MVVM：M→V V→M**

![1555570571241](C:\Users\user\AppData\Roaming\Typora\typora-user-images\1555570571241.png)

### 3.2 局部组件

代码耦合度变低，很好维护

局部组件**箴言**：声明子组件，挂子，用子

**声子：**var Vxxxx ={ template:'定义内容', }

**挂子：** var App ={ template:'', components:{Vxxxx}}

**用子：**new Vue（{ el:'',template:'<App></App>',components:{ App }}）

子传父

```javascript
//为了不与HTML5的header组件同名，所以起名Vheader
var Vheader = {
        template:'<div class="head">I am the header</div>'

    };

    var Vaside= {
        template:'<div class="vaside">I am the aside</div>'
    };

    var Vcontent = {
        template:'<div class="vcontent">I am the content</div>'
    };
    //1. 声明局部组件，入口组件！！！
    var App = {
        template: '<div class="main">' +
            '<Vheader />' +
            '<div class="main2">' +
            '<Vaside />' +
            '<Vcontent />' +
            '</div>'+
            '</div>',
        data(){ //data必须是函数
            return{
            }
        },
        components: {
            Vheader,
            Vaside,
            Vcontent
        }
    };

    new Vue({
        el:'#app',
        template:'<App />', //自定义组件
        data(){
            return{
            }
        },
        components:{ //2. 挂载组件
            App
        }
    })
```



### 3.3 全局组件

```javascript
//第一个参数是组件的名字，第二个参数是options
Vue.component('Vbtn',{
    template:'<button>按钮</button>',
})
//等于直接写入new Vue{}的components中
```

**组件的创建就是可复用性**

* 应用场景：对出使用的公共性功能组件，就可以注册成全局组件，减少冗余代码
* 全局API：Vue.component('组件名'，组件对象)

### 3.4 回顾

#### 3.4.1 如何使用vue

1. 引包

2. 创建实例化对象new Vue(options)

3. options

    

   ```JavaScript
           el:'#app',
           template:' ', //自定义组件
           data(){
               return{
               }
           },
           components:{ //2. 挂载组件
               App
           }
   ```

4. 插值语法：{{字符串}} {} {{变量名}}，设计的初衷只是内部做简单的语法

5. 指令系统

   v-text {{}}  innerText

   v-html  innerHTML

   v-if 和 v-show

   - v-if：如果为true 相当于appendChild

     ​	如果为false 相当于removeChild

   - v-show：如果为true 相当于 oDiv.style.display = 'block'

     ​	如果为false 相当于 oDiv.style.display = 'none'

6. 数组对象

   v-for = '{item,index} in arrs' 

   v-for 优先级 > v-if  v-show  {{}} v-html

   

   v-on:原生 js的事件名 = '逻辑' 

   v-on:原生 js的事件名 = '方法名' 该方法在methods中

   

   v-bind:标签中的原生属性

   v-bind:自定义的属性

   data ===> View 单向数据绑定

   

   v-model 只能应用在表单控件 value  UI控件

   **双向数据绑定：**

   ![1555580743301](C:\Users\user\AppData\Roaming\Typora\typora-user-images\1555580743301.png)

![1555588074909](C:\Users\user\AppData\Roaming\Typora\typora-user-images\1555588074909.png)

## 4 父子组件通信传值——props

### **4.1 父→子通信步骤：**

```javascript
//父→子通信步骤：
//1. 在父组件中先绑定自定义的属性名字 = posts
//2. 子要声明props:['自定义属性名"]来接收
//3. 收到了就是自己，可以随便用
```

### **4.2 子→父通信步骤：**

1. 在子组件中定义button按钮，绑定click 事件

2. 子组件methods中加入自定义事件，并调用父组件自定义方法：

   **this.$emit**('父组件中的子组件自定义方法')，第一个参数是触发自定义事件的名字；第二个参数是传进去的值

3. 定义父组件中自定义方法

![1555596555812](C:\Users\user\AppData\Roaming\Typora\typora-user-images\1555596555812.png)

# 花絮：引号嵌套问题！！

  JS中：*双引号内不能包含双引号，单引号内不能出现单引号。*

如果遇到多次嵌套时，有以下两个解决方案：

```javascript
//1. 使用转义符号“\”转义

template:'<div @click="school=\'home\'">组件一 {{school}}</div>',

//2. 使用es6模板字符串“``”

template: ` <div @click="school='home'">组件一 {{school}}</div> ` ,

//建议：vue中使用es6模板字符串。
  
```

### 4.3 子组件标签中输入内容散射到全局组件中<slot></slot>

```javascript
Vue.component('mySlot',{
    template: '<li>' +
        '预留的坑1' +
        '<slot name="two"></slot>' +
        '预留的坑2' +
        '<slot name="one"></slot>' +
        '<slot name="three"></slot>'+
        '</li>'
});
var App = {
    template: '<div>' +
        '<ul>' +
        '<mySlot>' +
        '<h1 slot="three">Mr Black</h1>' +
        '</mySlot>'+
        '<mySlot>' +
        '<h2 slot="one">填坑1</h2>' +
        '<h3 slot="two">填坑2</h3>' +
        '</mySlot>'+
        '</ul>' +
        '</div>',
};
```

![1555668063424](C:\Users\user\AppData\Local\Temp\1555668063424.png)

4.4 .native的使用

```JavaScript
var Vcontent = {
    template: '<div class="vcontent">I am the content' +
            //.native指原生，通过native给当前组件中的全局组件绑定事件
        '<Vbtn @click.native="add">登录</Vbtn>' +
        '<ul>' +
        '<li v-for="(item,index) in posts">' + '{{item.id}}' +
        '<p>{{item.title}}</p>' +
        '<p>{{item.content}}</p>' +
        '</li>' +
        '</ul>' +
        '</div>',
    props: ['posts'],
    methods:{
        add(){
            alert(1);
        }
    }
};
```

## 5 过滤器 — filters

**过滤器的作用**：对你当前的组件添油加醋

```JavaScript
//在组件内部使用filters:{
// 过滤器的名字:function(value){
// 内部一定要return
// }
// }
//调用过滤器：数据属性 | 过滤器的名字

var App = {
    data(){
        return{
            price:6
        }
    },
    template: '<div>' +
            '<input type="number" name="price" v-model="price">'+
            '<h3>{{price|myCurrentcy}}</h3>'+
        '</div>',
    filters:{
        myCurrentcy:function (value) {
            return '￥'+value;
        }
    }
};
```

## 6 v-watch监听器

一般监视单个数据，基本数据类型

```JavaScript
<div id="app">
    <input type="text" name="" v-model="msg">
    <h3>{{msg}}</h3>
</div>

    new Vue({
        el:'#app',
        data(){
            return{
                msg:'',
            }
        },
        watch:{
            msg:function (newV,oldV) {
                // console.log(newV,oldV)//新输入的和之前输入的值
                if (newV==='tusir'){
                    console.log('ok')
                }
            },
        }
    })
```

### 深度监听：复杂数据类型

```JavaScript
<div id="app">
    <button @click="stu[0].name='Rose'">切换</button>
    <h3>{{stu[0].name}}</h3>
</div>
	new Vue({
        el:'#app',
        data(){
            return{
               stu:[{name:'Jack'}]
            }
        },
        watch:{
           stu:{
                deep:true,//深度监听
                handler:function (newV,oldV) {
                    console.log(newV[0].name,oldV[0].name)
                }
            }
        }
    })
```

## 7 计算属性computed

computed属性：

- computed用来监控自己定义的变量，该变量不在data里面声明，直接在computed里面定义，然后就可以在页面上进行双向数据绑定展示出结果或者用作其他处理；
- computed比较适合对多个变量或者对象进行处理后返回一个结果值，也就是数多个变量中的某一个值发生了变化则我们监控的这个值也就会发生变化，举例：购物车里面的商品列表和总金额之间的关系，只要商品列表里面的商品数量发生变化，或减少或增多或删除商品，总金额都应该发生变化。这里的这个总金额使用computed属性来进行计算是最好的选择

### watch和computed的区别：？

- watch可以监听数据属性，并在内部做一些事，只能监听单个数据
- computed可以监听多个数据属性

### methods和computed的区别：？

- 

### computed_setter/getter

详见代码



## 8  组件的生命周期

- beforeCreate—组件创建之前，用处：加载loading动画

- created—组件创建之后，使用该组件就会调用created方法， 在created中可以操作后端数据，数据响应视图。

  ​		应用：发起ajax请求

- beforeMount—挂载数据到DOM之前会调用

- mounted—挂载数据到DOM之后会调用 Vue作用以后的DOM

  ![1555844407856](C:\Users\user\AppData\Local\Temp\1555844407856.png)

- beforeUpdate—更新DOM之前调用此钩子函数，应用：可以获取原始的DOM

- updated—更新DOM之后调用此钩子函数，应用：可以获取最新的DOM

- activated

- deactivated

- beforeDestroy—销毁前

- destroyed—销毁后

### <keep-alive></keep-alive>

需与activated、deactivated配合使用

```javascript
template: '<div>' +
        //vue的内置组件，能在切换过程中将状态保留在内存中，防止重复渲染DOM
    '<keep-alive>' +
    '<Test v-if="isShow"></Test>' +
    '</keep-alive>' +
    '<button @click="isShow=!isShow">改变生死</button>' +
    '</div>',
```



# 重点

### 组件的使用

父子组件的通信

过滤器

watch - computed

生命周期