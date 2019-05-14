[TOC]

# VUE组件相关

## 1 组件封装

1. cmponents中新建common文件夹
2. common中新建对应组件vue文件，如MyUl.vue
3. MyUl.vue中引入组件模板代码，和css、js代码，引出组件名和数据等

``` HTML
<template>
    <ul>
        <slot></slot>
        <!--<slot>容器-->
    </ul>
</template>
<script>
    export default {
    name:'my-ul',
    }
</script>
<style scoped>
ul {
  margin:0;
  padding: 0;
  overflow: hidden;
} 
</style>
```
4. main.js中引入全局组件

   ```JavaScript
   // 全局组件 开始
   import MyUl from './components/common/MyUl';
   import MyLi from './components/common/MyLi';
   Vue.component(MyUl.name,MyUl);
   Vue.component(MyLi.name,MyLi);
   ```

5. 修改使用全局组件的VUE文件中对应内容

   ```HTML
   <my-ul>
     <my-li v-for="(module,index) in modules" :key="index">
       <router-link :to="module.route">
         <span :class="module.className"></span>
         <div>{{module.title}}</div>
       </router-link>
     </my-li>
   </my-ul>
   ```

### 补充：组件的复用（兼容方式）

**！！！！！父传递给子的组件，子在接收成为自己的属性后，发生改变的话，不会触发视图更新**（所以做出相应改进，vue官方文档有更好的解决方案，有待学习）

来源：轮播图自定义组件（取自mint-ui，再自定义加工）

-  D:\github\myFirstVue\src\components\common\Swipe.vue
- D:\github\myFirstVue\src\components\Goods\GoodsDetail.vue
- D:\github\myFirstVue\src\components\Home.vue

组件的定义js方式

【注意】props传进来的参数名，不能与data中定义的变量同名！！！

```HTML
<mt-swipe-item v-for="(item,index) in imgs||my_imgs" :key="index" class="mint-swipe">
  <img :src="item.img||item.srr" alt="item.alt" style="width: 100%; height: 100%">
</mt-swipe-item>
```

```JavaScript
name: "my-swipe",
props: ['url','imgs'],
data() {
  return {
   my_imgs: [],//轮播图数据
        //my_imgs:this.imgs的修改不会影响外部的this.imgs的重新渲染
  }
},
created() {
  //兼容传递方式，若接收到url则使用axios请求，若未收到，则使用props接收到的父组件的数据
    //子接收父的属性再去做显示，也不能显示成功，内部执行时机问题
  if (this.url){
    this.$axios.get(this.url)
      .then(res => {
        this.my_imgs = res.data.message
      }).catch(err => {
      console.log(err)
    })
  }
}
```

1. 使用URL方式发送Axios请求，如：Home.vue中

2. ```HTML
   <my-swipe url="getlunbo.json"></my-swipe>
   ```

2. 不使用组件中定义的axios请求，如：GoodsDetail.vue中需要同步数据的情况

   ```html 
   <my-swipe :imgs="imgs"></my-swipe>
   <!--组件中绑定props属性  :imgs -->
   ```

**总结：**暂且不把不属于子属性混为一谈，可能造成不显示或更新失败的问题

**vuex不存在这些问题**



## 3 时间过滤器

来源：D:\github\myFirstVue\src\main.js

### 3.1 显示后台时间并过滤

将后台传送回来的时间数据进行格式重排

moment获取系统时间，不联网

1. 引入时间处理插件 — moment 

   npm install moment -D

2. 在main.js中引入插件，并配置全局过滤器

   ```JavaScript
   //定义全局过滤器  开始
   import Moment from 'moment'
   Vue.filter('convertTime',function (data,format) {
     return Moment(data).format(formatStr)
   })
   //定义全局过滤器 结束
   ```

3. 在对应插件中使用过滤器

### 3.2 将时间换算为据现在过去多久 （XXX前）





## 8 图片懒加载

[博客文章](https://www.cnblogs.com/liliangel/p/6122836.html)

来源：D:\github\myFirstVue\src\components\Photo\PhotoList.vue

```HTML
<img v-lazy="img.img_url">
```

```css
image[lazy=loading] {
  width: 40px;
  height: 300px;
  margin: auto;
}
```

**让插件来局部安装mint-ui的组件，减小整体文件大小：**

- mint-ui/lib/switch/→有单独的js和css 

- 详见项目：D:\github\myFirstVue\src\plugins\MyMintUIPlugin.js

  



## 9 定义拦截器，实现加载动画

来源：D:\github\myFirstVue\src\main.js

```JavaScript
//定义拦截器
//1：请求发起前显示loading open（）；
Axios.interceptors.request.use(function (config) {
  //不变配置：可变，可以设置公共的请求头操作
  MintUI.Indicator.open({
    text:'加载中...',
    spinnerType:'fading-circle'
  })
  return config  //config:{header}
})
//2：响应回来后关闭loading close（）
Axios.interceptors.response.use(function (response) {
  //response:{config:{},data:{},headers:{}}

  //接收响应头或者响应体重的数据，保存起来，供请求的拦截器中使用头信息操作
  MintUI.Indicator.close()
  console.log(response)
  return response
})
```
## 10 slot样式踩坑

来源：D:\github\myFirstVue\src\components\common\MyLi.vue

定义了全局组件<my-li>后，在其中插入容器slot

```HTML
<template>
    <li>
      <slot></slot>
      <slot name="icon" ></slot>
    </li>
</template>
<!--为slot中的img设置样式-->
<style scoped>
  /*slot中的图片设置*/
  img{
    width: 100%;
  }
</style>
```

使用全局组件

```HTML
<my-ul>
  <my-li v-for="(item,index) in imgs" :key="index">
    <img :src="item.src" slot="icon">
  </my-li>
</my-ul>
```

- 使用者本身可以对传入的slot元素设置样式
- 在接收这个DOM的组件中，能给slot以后的元素设置样式，而没有能给slot设置样式
- slot 样式管理孙，但没有管理子
- 因为slot 本身不会存在，不像router-view会自己加一个div，所以给他加样式加不上 
- 传递slot自己管理自己

## 11 [图片预览 v-preview](https://www.npmjs.com/package/vue-preview)（查看官方文档）

来源：D:\github\myFirstVue\src\components\Photo\PhotoDetail.vue

1. **下载插件**

```sql lite
npm i vue-preview -S
```

2. **安装配置**

   ```JavaScript
   import VuePreview from 'vue-preview'Vue.use(VuePreview)
   ```

3. **使用**

这里先看官方demo的图片：

slides绑定的slide1一共有6个属性，其中src，msrc以及w，h是一定要给的，不给就空白或者报错

w、h是查看图片的大小

![官方](https://ww2.sinaimg.cn/large/aced9e33gy1fz2wjyc62aj20fc0gxmxh.jpg)

所以在完成第一步，第二部后使用直接在你要循环data这个数组的地方这样写，slides绑定的就是那个数组对象

```HTML
<vue-preview class="preview" :slides="data"></vue-preview>
```



## 12 [用 async/await 来处理异步](https://www.cnblogs.com/SamWeb/p/8417940.html)

来源：D:\github\myFirstVue\src\components\Shopcart\Shopcart.vue

```javascript
async created(){
    //生成URL
  // let url = 'goods/getshopcarlist/'+Object.keys(test.goodsObj).join(',')
  let url = 'goods/getgoods1.json'
  let goodsList = Object.keys(test.goodsObj)
  console.log(goodsList)

  //这句话导致，按增加键时视图没有更新
  //Object.defineProperty(this,'shopcart'，{
  // set:function(){ //获知视图要更新
  //      //判断shopcart元素 是否还有属性，那这些对象的属性都要这么干
  //      //
  // }
  // })

  //也就是这个行为会将alllist中所有属性进行监视，完成属性的响应式，页面改
  let alllist = (await this.$axios.get(url)).data.message
  this.getCartList(alllist)
  // console.log(this.shopcart)


  //给数组元素添加属性（使用await的时候使用ES6中的map/each/find/findIndex是没有效果）
  for (var i=0;i<this.shopcart.length;i++) {
    let goods = this.shopcart[i]
    let num = test.goodsObj[goods.id]
    if (num){
      //手动完成响应式
      this.$set(goods,'num',num)
      // goods.num = num
      this.$set(goods,'isSelected',true)
      // goods.isSelected = true
    }
  }
}
```



## 13 购物车小球动效

[单元素/组件的过渡](https://cn.vuejs.org/v2/guide/transitions.html#%E5%8D%95%E5%85%83%E7%B4%A0-%E7%BB%84%E4%BB%B6%E7%9A%84%E8%BF%87%E6%B8%A1)

![](D:\文件\前端\笔记\vueStudy\images\父子组件加载过程.png)

Vue 提供了 **`transition` 的封装组件**，在下列情形中，可以给任何元素和组件添加进入/离开过渡

- 条件渲染 (使用 `v-if`)
- 条件展示 (使用 `v-show`)
- 动态组件
- 组件根节点

这里是一个典型的例子：

```HTML
<div id="demo">
  <button v-on:click="show = !show">
    Toggle
  </button>
  <transition name="fade">
    <p v-if="show">hello</p>
  </transition>
</div>
```

```JavaScript
new Vue({
  el: '#demo',
  data: {
    show: true
  }
})
```

```css
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
```

可以在属性中声明 JavaScript 钩子

```HTML
<transition
  v-on:before-enter="beforeEnter"
  v-on:enter="enter"
  v-on:after-enter="afterEnter"
  v-on:enter-cancelled="enterCancelled"

  v-on:before-leave="beforeLeave"
  v-on:leave="leave"
  v-on:after-leave="afterLeave"
  v-on:leave-cancelled="leaveCancelled"
>
  <!-- ... -->
</transition>
```
## 14 父子组件传参，购物车小球数量显示（EventBus实例）

来源：

1. D:\github\myFirstVue\src\MyBus.js
2. D:\github\myFirstVue\src\App.vue
3. D:\github\myFirstVue\src\components\Goods\GoodsDetail.vue

- 1

1. ```javascript
   import Vue from 'vue'
   let MyBus = new Vue()
   export default MyBus
   ```

- 2

  ```JavaScript
  import MyBus from './MyBus'
  
  created() {
      MyBus.$on('addShopcart',(num) =>{
        this.totalNumber += num
      })
    }
  ```

- 3 在要操作的地方引入父组件方法

  ```JavaScript
  import MyBus from '@/MyBus'
  
  //这是transition组件定义的方法，小球消失后的动作
  afterEnter(){
            this.isShow = false
            MyBus.$emit('addShopcart',this.buyNumber)
          }
  ```



## 15 路由切换的淡入淡出效果

1. 除去头标签和底部导航栏，中间的路由部分实现动效，用vue框架的<transition>组件实现

```HTML
<!--out-in 表示元素先离开，再进入，默认同进同出-->
<transition name="fade" mode="out-in">
  <router-view class="tmpl"></router-view>
</transition>
```

```css
/*对应的css*/
.fade-enter-active, .fade-leave-active {
  transition: opacity .3s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
```

