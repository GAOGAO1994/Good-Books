[TOC]

# webpack

main.js 是整个项目的入口文件

App.js 存放App组件，最后 export default App; 传出

在main.js 中引用：import App from '路径';

同时引用Vue.js

将vue、App、main打包，最后在index.html中引入build.js

**命令行方式**：

![1556020247793](c:\Users\user\AppData\Local\Temp\1556020247793.png)

**package.json中配置**

![1556021449592](C:\Users\user\AppData\Local\Temp\1556021449592.png)

然后，执行npm run build

![1556021525527](C:\Users\user\AppData\Local\Temp\1556021525527.png)

### 1 webpack打包模块的源码执行顺序

1. 把所有模块的代码放入到函数中，用一个数组保存起来
2. 根据require时传入的数组索引，能知道需要哪一段代码
3. 从数组中，根据索引去除包含我们代码的函数
4. 执行该函数，传入一个对象 module.exports
5. 我们的代码，按照约定，正好是用module.exports = 'xxxx' 进行赋值
6. 调用函数结束后，module.exports从原来的空对象，就有值了
7. 最终return module.exports; 作为requires函数的返回值

build.js解析

```javascript
(function(modules) {
...
}([
    /* 0 对window模块的处理*/
     (function(module, exports){...})
     /* 1 对main.js模块的处理，加载了App.js*/
      (function(module, __webpack_exports__, __webpack_require__) {...})
      /* 2 vue的源码，，导出了一个Vue的实例对象 vue.esm.js-完整编译版的vue文件*/
      (function(module, exports, __webpack_require__){...})
      /* 3 vue对DOM的异步更新机制*/
      (function(module, exports, __webpack_require__){...})
      /* 4 DOM异步处理*/
       (function(module, exports, __webpack_require__) {...})
       /* 5 DOM异步处理*/
       (function(module, exports) {...})
       /* 6 把es6代码转成严格的js代码,App.js的解析*/
       (function(module, __webpack_exports__, __webpack_require__){...})
]);
```

### 2 导出方式

``` JavaScript
//声明并导出
export var num1 = 2;//作为一整个对象的key到处

//声名再导出
var num2 = 3;
export {num2};

//导出方法
export function add(x,y) {
  return console.log(x+y);
};
```

main.js中接收

```JavaScript
/*整个模块加载*/
import * as obj from './App';
```

### 3 配置 webpack.config.js 文件

webpack.config.js ——

webpack.dev.config.js —— 开发环境下的

webpack.prod.config.js —— 生产环境下的

```JavaScript
//设置 对应的package.json
"scripts": {  "build": "webpack",  "dev":"webpack --config ./webpack.dev.config.js",  "prod": "webpack --config ./webpack.prod.config.js"},
```

watch:true,//文件监视改动，自动产出build.js



### 4 webpack 打包 css 文件

*loader* 让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript）。loader 可以将所有类型的文件转换为 webpack 能够处理的有效[模块](https://www.webpackjs.com/concepts/modules)，然后你就可以利用 webpack 的打包能力，对它们进行处理。 

> *注意，loader 能够 `import` 导入任何类型的模块（例如 `.css` 文件），这是 webpack 特有的功能，其他打包程序或任务执行器的可能并不支持。我们认为这种语言扩展是有很必要的，因为这可以使开发人员创建出更准确的依赖关系图。* 

webpack.dev.config.js文件中

```JavaScript
module:{
    loaders: [
        {
            //遇到后缀为.css的文件，webpack先用css-loader加载器去解析这个文件
            //最后计算完的css，将会使用style-loader生成一个内容为最终解释完的css代码的style标签，放到head标签里。
            //webpack在打包过程中，遇到后缀为css的文件，就会使用style-loader和css-loader去加载这个文件
            test:'/\.css$/',   //正则
            loader:'style-loader!css-loader'
        }
    ]
}
```
### 5 loader的配置

webpack最终会将每个模块打包成一个文件，因此我们样式中的URL路径是相对入口HTML的，而不是相对于原css文件所在路径的，这就会导致图片引入失败，这个问题是用file-leader解决的，file-leader可以解析项目中的URL引入（不仅限于css），根据我们的配置，将图片拷贝到相应的路径，再根据我们的配置，修改打包后文件引用路径，使之指向正确的文件。

简易，对于比较小的图片，使用base64编码，可以减少一次图片的请求，那么对于比较大的图片，使用base64就不合适，编码会和HTML连在一起，一方面可读性差，另一方面加大了HTML页面的大小，反而加大了下载页面的大小，得不偿失，因此设置一个合理的limit是必要的

```JavaScript
npm install --save-dev css-loader
//引入loader，同时去配置package.json和webpack.dev.config.js中相关配置
```

### 6 less文件

**命令行**：npm install less-loader

​		npm install less

**package.json** 加入"less-loader": "^4.1.0","less": "^3.9.0"

**webpack.dev.config.js**中配置：

​		{    test:/\.less$/,   

 		loader:'style-loader!css-loader!less-loader'}

**main.less**：

```less
@imgPath : './myGirl.jpg';
body{
  background-image:url(@imgPath);
}
```

### 7 文件整理

将静态文件存入src文件夹下

webpack生成的build.js存入dist文件夹下

配置webpack.dev.config.js：

```JavaScript
var path = require('path');

output:{
        path:path.resolve('./dist'), //相对转绝对
        filename:'build.js'
    },
```

dist文件夹下复制index.html文件，修改路径

### 8 html-webpack-plugin插件的使用

会自动生成dist文件夹下的HTML

cmd下载：npm i html-webpack-plugin -D

修改webpack.dev.config.js

```JavaScript
const HtmlWebpackPlugin = require('html-webpack-plugin');

 plugins:[
        //放插件的，插件的执行顺序，与元素索引有关
        new HtmlWebpackPlugin({
            template:'./src/index.html',//参照物
        })
    ]
```

### 9 webpack-dev-server 实时监听

下载模块：

npm i install webpack-dev-server@2.9.0  不同版本间的执行差异

常用配置参数

- --help 自动打开浏览器
- --hot 热更新，不在舒心的情况下替换css样式
- --inline 自动刷新
- --port 9999 指定端口
- --process 显示编译进度

在package.json中配置

### 10 ES6的解析

模块介绍

**babel-core**

javascript babel-core的作用是吧js代码分析成ast（抽象语法树），方便各个插件分析语法进行相应的处理，有些新语法在低版本js中是不存在的，如箭头函数，rest函数、函数默认值等，这种语言层面的不兼容只能通过将代码转换为ast，分析其语法后再转为低版本js

abel转译器本身，提供了babel的转译API，如babel.transform等，用于对代码进行转译，像webpack的babel-loader就是调用这些API来完成转译过程的。

**babel-loader**

在将es6的代码transform进行转译，就是通过babel-loader来完成

babel-preset-env

如果要自行配置转译过程中使用的各类插件，就太痛苦了，所以babel官方帮我们做了一些预设的插件集，称之为preset，这样我们只需要使用对应的preset就可以了。以js标准为例，babel提供了如下一些preset：

- es2015/2016/2017
- env-最新的标准

es20xx的preset只转译该年份批准的标准，而env则代指最新的标准，包括了lateset和es20xx各年份，另外还有stage-0到stage-4的标准成型之前的各个阶段，这些都是实验版的preset，建议不要使用。

babel-plugin-transform-runtime、babel编译时只转换语法，几乎所有的编译所有新的JavaScript语法，但是不会转化DOM里面不兼容的API，比如Promise.Set.Symbol.Array.from.async等等一些API

安装模块：npm install babel-core babel-loader babel-preset-env ba
bel-plugin-transform-runtime -D

在webpack-dev-config.js配置

```JavaScript
{
     // 处理es6,7,8
    test: /\.js$/,
    loader: 'babel-loader',
    exclude:/node_modules/,//依赖的模块，不需要处理
    options: {
         presets: ['env'], //处理关键字
         plugins: ['transform-runtime'], //处理函数
    }
}
```

### 11 单文件引入

下载包 npm install vue-loader@14.1.1 vue-template-compiler@2.5.17 -D

创建App.vue文件

```vue
<template>
   <!--页面的结构-->
   <div>我是入口组件{{msg}}</div>
</template>

<script>
   <!--业务逻辑代码-->
   export default {
      data(){
         return{
            msg:'hello App.vue'
         }
      }
   }
</script>

<style>
<!--css 样式-->
   body{
   background-color: #417FC8;
}
</style>
```

main.js文件：

```JavaScript
import Vue from '../node_modules/.staging/vue-11c0c667/dist/vue';
import App from './App'

// 组件  .vue

new Vue({
   el:'#app',
    render:c => c(App)//需要用函数去渲染模块，vue2.0新功能。使用虚拟DOM来渲染节点，提升性能、
    //因为他是通过js计算，铜鼓是用createElement(h)来创建dom节点，createElement(h)是render的和核心方法
    
    //vue编译过程template里面的节点解析成虚拟DOM

    //下面的无法解析
   // components:{
   //     App
   // } ,
   //  template:'<App></App>'
});
```

**使用了vue-cil 之后基本不用配置webpack了~~~**



1. v-for中key
2. 虚拟DOM
3. $emit 
4. $on
5. vue-router的导航守卫 动画效果 linkActiveClass
6. restful接口 画图
7. mock.js