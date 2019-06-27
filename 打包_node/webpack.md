[TOC]

# [webpack 4.x](https://malun666.github.io/aicoder_vip_doc/#/pages/vip_2webpack)

## webpack是什麽

打包工具，根据入口一层层打包

**webpack天生支持es6的语法**

## webpack安装

1. 首先创建项目文件夹
2. npm init，创建package.json，配置文件
3. 安装webpack到本地node_module

- 不建议全局安装，因为对新旧版本需求不同
- 首先，下载相关依赖，依赖会自动添加到package.json相关路径下

```JavaScript 
npm install webpack -D 
npm install -D webpack-cli //4.x版本需要
npm i lodash -P 
```

4. 创建文件夹：

- dist：打包文件夹，里面创建index.html文件
- src/index.js：代码存放文件夹。index.js入口文件

5. 写入口文件的内容

   - 引入相关的包，写功能，如下例：

     ```JavaScript 
     import _ from 'lodash';
     
     function createDomElement() {
         let dom = document.createElement('div');
         dom.innerHTML = _.join(['aicoder.com','good','text','']);
         return dom;
     }
     
     let divDom = createDomElement();
     
     document.body.appendChild(divDom);
     ```

- - 根目录下创建webpack.config.js文件，配置webpack入口出口等

    ```JavaScript 
    const path = require('path');
    
    module.exports = {
        entry:'./src/index.js', // 入口文件
        mode:'development', //开发模式
        output:{
            filename:'main.js',
            path:path.resolve(__dirname,'dist') // __dirname:当前路径
        }
    };
    ```

6. 执行 npx webpack  （npx相当于执行二进制文件） ，将index.js和lodash打包成一个文件
7. - 此时，dist文件夹下生成了main.js文件，代码很多，
8. 此时在index.html中引入main.js文件，打开chrome查看效果，之前写的js文件都被自动加载到main.js中了

## webpack加载非js文件

1. 先安装 css-loader（解析css文件），style-loader（转换成style标签注入到HTML中）

2. 创建 src/style/index.css 文件，写入一些样式

3. 在index.js中引入样式并使用

4. 修改webpack.config.js文件，添加以下代码：

   ```JavaScript 
   module:{
       rules:[
           {
               test: /\.css$/, 
               //检测引入的文件后缀，当引入的文件是css文件时。。。
               use:['style-loader','css-loader']
           }
       ]
   }
   ```

5. 重新执行 npx webpack

6. :warning:注入时，css样式都是通过js脚本注入的！！！！，所以此时打开index.html刷新，样式更新了。

7. F12，查看网页源代码时，style标签被插入到head标签内

## webpack module补充

1. webpack模块导入支持：

- - ES2015 import语句
- - CommonJS require（）语句
- - AMD define 和 require 语句
- - css/sass/less 文件中的 @import 语句
- - 样式 （url(...)) 或 HTML 文件 (<img src=...>)中的图片链接 （img url）
- - **-只要有对应的loader，都可以支持**

2. module.noParse：防止webpack解析任何与给定正则表达式相匹配的文件，忽略文件中不应该含有import，require，define的调用，或任何其他导入机制。忽略大型的library可以提高构建性能。

3. module.rules：创建模块时，匹配请求的规则数组，这些规则能够修改模块的创建方式。这些规则能够对模块（module）应用loader，或者修改解析器（paeser）。:star:如添加css解析规则，其中一个对象表示一个规则。

   `{ test: Condition }`：匹配特定条件。一般是提供一个正则表达式或正则表达式的数组，但这不是强制的。

   `{ include: Condition }`：匹配特定条件。一般是提供一个字符串或者字符串数组，但这不是强制的。

   `{ exclude: Condition }`：排除特定条件。一般是提供一个字符串或字符串数组，但这不是强制的。

   `{ and: [Condition] }`：必须匹配数组中的所有条件

   `{ or: [Condition] }`：匹配数组中任何一个条件

   `{ not: [Condition] }`：必须排除这个条件

   ```JavaScript 
   {
     test: /\.css$/,
     include: [
       path.resolve(__dirname, "app/styles"),
       path.resolve(__dirname, "vendor/styles")
     ]
   }
   ```



## webpack加载sass文件

1. ```JavaScript 
   {
       test: /\.(sc|c|sa)ss$/,
       use:['style-loader','css-loader','sass-loader']
   }
   ```

2. 在src/style/a.scss写样式

3. 在index.js中引入样式

4. npx webpack，:warning:此时报错，需要安装node-sass，按要求安装即可

5. sass样式加载成功

## webpack打包指令设置

- 我们一直在使用npx webpack打包文件

- 可以在package.json中配置打包命令

  ```JavaScript 
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "npx webpack"
  }
  ```

- 此时 控制台输入 npm run build也可以进行打包

- :star:同理可得，我们为什么一直能使用npm run dev，来运行vue项目

## webpack 处理css相关操作

### webpack 创建sourcemap

-  css-loader 和 sass-loader 都可以通过 options 设置启用 sourcemap

- ```JavaScript 
  {
    loader: 'css-loader',
    options: {
      sourceMap: true
    }
  }
  ```

- 

### postCSS 处理 loader （附带：添加 css3 前缀）

PostCSS是一个 css 的预处理工具，可以帮助我们：给css添加前缀，样式格式校验（stylelint），提前使用css的新特性，比如：表格布局，更重要的是可以实现css的模块化，防止css样式冲突。！！！

- 安装：npm i -D postcss-loader --save

- 需要什么，安装什么：

- - npm install autoprefixer --save-dev  ，添加前缀

- - postcss-cssnext：写css4的语言，并能配合autoprefixer 进行浏览器兼容的不全，支持嵌套语法
  - precss：类似scss语法，
  - postcss-import：@import css文件的时候让webpack监听并编译

- 在webpack.config.js中添加：建议直接复制，不要自己写，容易出错:heavy_exclamation_mark:

- ```JavaScript 
  {
      loader: 'postcss-loader',
      options: {
          ident: 'postcss',// 没什么意义，微标识符
          sourceMap: true,
          plugins: (loader) => [
              require('autoprefixer')({browsers: ['> 0.15% in CN']})
              // 添加前缀！！
          ]
      }
  }
  ```

- 运行，刷新页面，打开控制台查看元素样式，此时添加了前缀

  1. ​    display: -webkit-box;
  2. ​    display: -ms-flexbox;
  3. ​    display: flex;

### :question:样式表抽离成专门的单独文件并且设置版本号

此时是生产模式，重新创建webpack.product.config.js，mode改为development

- 加入插件 MiniCSSExtractPlugin，在配置文件中引入

  const MiniCSSExtractPlugin = require('mini-css-extract-plugin')

- 判断当前环境是开发环境还是部署环境，主要是 mode属性的设置值。 

  const devMode = process.env.MODE_ENV !== 'production'

- 省略配置文件内容，**查看主标题中的教案**

- 最后配置运行指令，package.json中

- ```
  "dist": "npx webpack --config webpack.product.config.js"
  ```

- 此时css文件单独生成在dist文件夹中，需要在index.html中引入

  ``` HTML 
  <link type="text/css" rel="stylesheet" href="./main.css">
  ```

### 压缩css

生产环境中才需要压缩

### js压缩



### 解决 CSS 文件或者 JS 文件名字哈希变化的问题

要注意修改，模板html，记得添加，注意路径等细节

### 清理 dist 目录

引入插件，在plugin中添加：括号中不用传参数，会自动清理！

```JavaScript 
new CleanWebpackPlugin(),
```

### [加载图片与图片优化

解决方案：`file-loader`处理文件的导入 

那更进一步，图片如何进行优化呢？

`image-webpack-loader`可以帮助我们对图片进行压缩和优化。

### [更进一步处理图片成 base64

`url-loader`功能类似于 file-loader，可以把 url 地址对应的文件，打包成 base64 的 DataURL，提高访问的效率

```JavaScript 
{
            loader: 'url-loader', // 根据图片大小，把图片优化成base64
            options: {
              limit: 10000
            }
          },
```



## 开发相关辅助

### [合并两个webpack的js配置文件

开发环境(development)和生产环境(production)配置文件有很多不同点，但是也有一部分是相同的配置内容，如果在两个配置文件中都添加相同的配置节点， 就非常不爽。

**`webpack-merge` 的工具可以实现两个配置文件进合并**，这样我们就可以把 开发环境和生产环境的公共配置抽取到一个公共的配置文件中。

例如：project

```JavaScript 
  webpack-demo
  |- package.json
- |- webpack.config.js		// 
+ |- webpack.common.js		// 公共配置文件
+ |- webpack.dev.js		// 开发环境配置文件
+ |- webpack.prod.js	// 生产环境配置文件
  |- /dist
  |- /src
    |- index.js
    |- math.js
  |- /node_modules
```

webpack.dev.js

```JavaScript 
+ const merge = require('webpack-merge');
+ const common = require('./webpack.common.js');
+
+ module.exports = merge(common, {
+   devtool: 'inline-source-map',
+   devServer: {
+     contentBase: './dist'
+   }
+ });Copy to clipboardErrorCopied
```

webpack.prod.js

```JavaScript 
+ const merge = require('webpack-merge');
+ const UglifyJSPlugin = require('uglifyjs-webpack-plugin');
+ const common = require('./webpack.common.js');
+
+ module.exports = merge(common, {
+   plugins: [
+     new UglifyJSPlugin()
+   ]
+ });
```



### js 使用 source map （开发环境）

当 webpack 打包源代码时，可能会很难追踪到错误和警告在源代码中的原始位置。例如，如果将三个源文件（a.js, b.js 和 c.js）打包到一个 bundle（bundle.js）中，而其中一个源文件包含一个错误，那么堆栈跟踪就会简单地指向到 bundle.js。 

使用 `inline-source-map` 选项，这有助于解释说明 js 原始出错的位置。 

+ ```JavaScript 
  devtool: 'inline-source-map', // 配置文件，一级
  ```

  

### 监控文件变化，自动编译。使用观察模式

每次修改完毕后，都手动编译异常痛苦。最简单解决的办法就是启动`watch`。 

```node 
npx webpack --watch 
```

当然可以添加到 npm 的 script 中

package.json

```
"scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
+     "watch": "npx webpack --watch --config",
      "build": "npx webpack"
    },
```

**但是不完美，还是需要手动保存，ctrl+s**

### 使用 webpack-dev-server 和热更新   开发阶段

webpack-dev-server 为你提供了一个简单的 web 服务器，并且能够**实时重新加载**(live reloading)。 

1. 安装

```
npm install --save-dev webpack-dev-server
```

2. webpack.config.js 

```JavaScript 
+ const webpack = require('webpack');
。。。
    devServer: {
      contentBase: './dist',
+     hot: true
    },// 一级
    +     new webpack.NamedModulesPlugin(),  // 更容易查看，(patch)的依赖，plugins中
+     new webpack.HotModuleReplacementPlugin()  // 替换插件，plugins中
```

3. 启动此 webserver：打开后要手动输入相对地址？？？？

```
webpack-dev-server --open
```

- ```JavaScript 
  // 官网更多配置
  devServer: {
    clientLogLevel: 'warning', // 可能的值有 none, error, warning 或者 info（默认值)
    hot: true,  // 启用 webpack 的模块热替换特性, 这个需要配合： webpack.HotModuleReplacementPlugin插件
    contentBase:  path.join(__dirname, "dist"), // 告诉服务器从哪里提供内容， 默认情况下，将使用当前工作目录作为提供内容的目录
    compress: true, // 一切服务都启用gzip 压缩
    host: '0.0.0.0', // 指定使用一个 host。默认是 localhost。如果你希望服务器外部可访问 0.0.0.0
    port: 8080, // 端口
    open: true, // 是否打开浏览器
    overlay: {  // 出现错误或者警告的时候，是否覆盖页面线上错误消息。
      warnings: true,
      errors: true
    },
    publicPath: '/', // 此路径下的打包文件可在浏览器中访问。
    proxy: {  // 设置代理
      "/api": {  // 访问api开头的请求，会跳转到  下面的target配置
        target: "http://192.168.0.102:8080",
        pathRewrite: {"^/api" : "/mockjsdata/5/api"}
      }
    },
    quiet: true, // necessary for FriendlyErrorsPlugin. 启用 quiet 后，除了初始启动信息之外的任何内容都不会被打印到控制台。这也意味着来自 webpack 的错误或警告在控制台不可见。
    watchOptions: { // 监视文件相关的控制选项
      poll: true,   // webpack 使用文件系统(file system)获取文件改动的通知。在某些情况下，不会正常工作。例如，当使用 Network File System (NFS) 时。Vagrant 也有很多问题。在这些情况下，请使用轮询. poll: true。当然 poll也可以设置成毫秒数，比如：  poll: 1000
      ignored: /node_modules/, // 忽略监控的文件夹，正则
      aggregateTimeout: 300 // 默认值，当第一个文件更改，会在重新构建前增加延迟
    }
  }
  ```

### JS启用babel转码，生产&开发都需要

为了兼容老式的浏览器（IE8、9）我们需要把最新的ES6的语法转成ES5的。那么`babel`的loader就出场了。 

安装

```
npm i -D babel-loader babel-core babel-preset-env
```

用法

在webpack的配置文件中，添加js的处理模块。

```
module: {
  rules: [
    {
      test: /\.js$/,
      exclude: /(node_modules)/,  // 加快编译速度，不包含node_modules文件夹内容
      use: {
        loader: 'babel-loader'
      }
    }
  ]
}
```

然后，在项目根目录下，添加babel的配置文件 `.babelrc`.

`.babelrc`文件如下：

```
{
  "presets": ["env"]
}
```

:warning:babel-loader和babel-core版本不对应所产生的， babel-loader 8.x对应babel-core 7.x babel-loader 7.x对应babel-core 6.x 



## 解析(resolve)

配置模块如何解析。比如： `import _ from 'lodash'` ,其实是加载解析了lodash.js文件。此配置就是设置加载和解析的方式。

- `resolve.alias`

创建 import 或 require 的别名，来确保模块引入变得更简单。例如，一些位于 src/ 文件夹下的常用模块：

## 外部扩展(externals)

externals 配置选项提供了「从输出的 bundle 中排除依赖」的方法。 [文档](https://webpack.docschina.org/configuration/externals/)

例如，从 CDN 引入 jQuery，而不是把它打包：

## 打包分析优化



















