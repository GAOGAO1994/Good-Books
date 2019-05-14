[TOC]

# mixin插件 & history及原理

## 1 可配置性插件mixin

**插件：**是为了代码优雅，好维护

**配置：**是为了灵活

来源：D:\github\mixinDemo     [创建方式参考](D:\文件\前端\笔记\vueStudy\创建项目相关.md) (2.1)

mixin不用下载特殊的包，内置就有

- 将公共的行为C：混入（杂交）
- 发请求C的行为 混入 到组件A和B之中
  - 将行为混入到 **所有组件**中

### 1.1 创建过程

1. 创建组件my_mixin.js

   ```JavaScript
   export default {
     created(){
       setTimeout(() =>{
         console.log(`组件：${this.$options.name}的请求发出去了`)
         // this.$options就相当于整个组件，得到的是父组件的名字
       },1000)
     }
   } // 组件的影子
   ```

2. 在其他组件中引用

   ```JavaScript
   import mixin from '../my_mixin' // 先引入文件
     export default {
         name: "son",
       mixins:[mixin],  // 使用自定义组件
     }
   ```

3. 一个个引入很麻烦的话，可以在main.js中定义，使所有的VUE实例**继承**

   **:exclamation: :heavy_exclamation_mark: 此时，自定义组件的行为会混入到所有组件中**

   ```JavaScript
   // 所有的Vue的实例继承
   import Mymixin from '../my_mixin'
   Vue.mixin(Mymixin)
   ```

### 1.2 :star:自定义插件是一定要有install函数

- 第一步：创建插件xxx.js

  ```JavaScript
  // 1.引入其他需要的插件必须要顶级进行
  import Header from 'mint-ui/lib/header';//默认也会查找index.js
  import 'mint-ui/lib/header/style.css';
  // 必须具备install函数
  function MyInstaller(options) {
    //  options操作，保存选项
  }
  
  // 核心点！！！：必须具备install函数
  MyInstaller.install = function (Vue) {
    Vue.component(Header.name, Header);
  //js组件一般是使用该对象提供的方法，有对象即可
  //挂载到Vue的原型上，供所有组件实例对象使用(this)
    Vue.prototype.$toast = Toast;   // 组件this.$toast
  }
  export default MyInstaller
  ```

- 第二步，在main.js中引入，并使用该组件

  ```JavaScript
  import xxxx from '@/plugins/xxx'
  Vue.use(xxxx)
  ```

### 1.3  :star:面试加分:star:mixin插件用法案例：plugin_mixin_vuex_config

来源：D:\github\plugin_mixin_vuex_config

[创建方式参考](D:\文件\前端\笔记\vueStudy\创建项目相关.md) (2.2)

```
 Please pick a preset: Manually select features
 (本例中选择：vuex、router，babel)
Use history mode for router? (Requires proper server setup for index fallback in productio
n) No
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In dedicated config f
iles
? Save this as a preset for future projects? (y/N)
```

- 此时项目中多了个public文件夹（公共资源），默认将static放入public下
- :warning:router.js中，About组件的引入方式是**路由懒加载**方式

```JavaScript
{
  path: '/about',
  name: 'about',
  // route level code-splitting
  // this generates a separate chunk (about.[hash].js) for this route
  // which is lazy-loaded when the route is visited.
  component: () => import(/* webpackChunkName: "about" */ './views/About.vue')// 路由懒加载
}
```

#### 1.3.1 :star::warning:main.js中使用组件，可以传入参数，但相应的要大改造，否则会报错：找不到install函数

**方案一：**

```JavaScript
Vue.use(new MyPlugin(['about','home']))
// 传入的是使用该插件的组件
```

```JavaScript
// MyPlugin.js 
// 可以做一个对象 或 函数，一般是函数
let myOptions

// options接收的是数组。。。？？？
function myPlugin(options) {
    myOptions = options
    return this  // 当Vue.use(MyPlugin(['','']))有参数传入时，需要添加
    //VUE引用时就要加上new：Vue.use(new MyPlugin(['','']))
}

// 该对象具备install函数
// myPlugin.install = function (Vue) {  // 当Vue.use(MyPlugin)未传入参数时
myPlugin.prototype.install = function (Vue) { // 当Vue.use(MyPlugin(['','']))有参数传入时，需要添加
    Vue.mixin({
        created() {
            // 判断当前的组件名， ！！！当Vue.use(MyPlugin)未传入参数时使用以下判断
            // if(this.$options.name === 'home' || this.$options.name === 'about'){
            //     // 根据Vuex去调用，来改变数据（谁改？）
            //     this.$store.dispatch('addByAct')
            //     // console.log(this.$options.name)
            // }

            // Vue.use(MyPlugin(['','']))有参数传入时
            myOptions.forEach(opt => {
                // 必须使用箭头函数，取消this绑定
                if (this.$options.name === opt) {
                    this.$store.dispatch('addByAct')
                }
            })
        }
    })
}

export default myPlugin
```

**方案二：**

:star:只修改一处：

```JavaScript
function myPlugin(options) {
    myOptions = options
    return myPlugin  
}
```



## :question:install函数的意义

> install函数的意义？Vuex注册的时候，必须要暴露出这个方法，供Vue使用。install函数做了什么？判断Vuex是否已经注册，注册过就不再注册，Vue2.0给每个Vue组件的beforeCreate生命周期中增加vuexInit函数，给每个Vue组件上挂载一个$store对象，全部都指向Vuex的Store实例化的对象。 