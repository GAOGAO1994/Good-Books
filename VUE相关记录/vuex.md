[TOC]

# [vuex管理全局共享数据](https://vuex.vuejs.org/zh/guide/getters.html)（官方文档）

## 1 简单介绍，5个部分

 全局vuex  

A   B   C 组件各自更改共享数据，不必担心组件通信

**数据存储仓库：state**

1. 获取到仓库可以直接改内部的数据（但这样不好，最好使用3的getter方式获取）

   ```JavaScript
   // store.js中的共享数据定义
   state:{
     num: 5
   },
   ```

2. 改变方式（mutation）：增、删、改      （与methods结合使用）

   ```JavaScript
   // store.js中
   mutations:{
        // 更改的方式，更改也和state很近，直接用
     addNum(state){
       state.num++
     },
     // mutation对state的操作只能是同步的，异步的mutation
     addByNumAsync(state,num){
       // 只能是同步，否则丢失记录
       setTimeout(function () {
         state.num += num
       },0)
     }
   }
   ```

```JavaScript
this.$store.commit('addNum') //使用commit调用仓库的方法，写在methods函数中
```

> **为什么Vuex中必须要通过commit提交mutation？**
>
> 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutations 非常类似于事件：<u>每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)</u>。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 **state 作为第一个参数** 
>
> 你不能直接调用一个 mutation handler。这个选项更像是事件注册：“当触发一个类型为 increment 的 mutation 时，调用此函数。”要唤醒一个 mutation handler，你需要以相应的 type **调用 store.commit 方法** 
>
> 我们通过提交 mutation 的方式，而非直接改变 store.state.count，`是因为我们想要更明确地追踪到状态的变化`。 

3. 获取方式（getter）：获取num、age。。。（与computed结合使用）

```JavaScript
// store.js中
getters:{
  getNum(state){ // 获取器离state很近，可以直接拿来用
    return state.num
  }
}
```

```javascript
computed:{
  getMyNum(){
    return this.$store.getters.getNum
  }
}
```

4. 业务行为（action）：调用改变方式

- - action中有可能去发送请求，存在异步行为（例：用户下单优惠，还要再发送请求判断其是否享受该优惠）
- - :warning:万一异步行为存在于mutation中，会导致**本次**改动数据的**记录丢失**

this.$store.dispatch(某action数据)

```JavaScript
// store.js中
actions:{
  // $store.getters||commit||state,data
  // 接收的参数是整个store对象，或者使用解构赋值{commit}
  addByNumAction({commit}, num) {
    setTimeout(function () {
      commit('addByNum',num)
    }, 0)
  }
}
```

```JavaScript
addByNumAction(){
  // 异步处理需要调用Action
  this.$store.dispatch('addByNumAction',5)
}
```

5. 多模块（modules）：多个以上模块

   将new Vuex.Store({})中的数据保存到另一个js文件：num1.js

   ```javascript
   export default {
     // ...
   }
   ```

   回到store.js中

   ```JavaScript
   // 引入module 1
   import Num from './num1'
   
   let store = new Vuex.Store({
     // 出现同名函数或变量的时候，为了保护其不被覆盖
     // 在文档中还有某个命名空间的概念
     modules:{
       // 不会影响使用，都是大家的
       num:Num
     }
   })
   ```



## 2 项目案例- 简单接触

可查看项目：D:\github\vuexdemo

### `mapGetters` 辅助函数

`mapGetters` 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：

```JavaScript
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```

如果你想将一个 getter 属性另取一个名字，使用对象形式：

```JavaScript
mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```



## chrome插件

- devTools 可以看到 vuex 数据快照状态



## 3 开发中必须会踩的坑

- 业务中给 state 添加属性， **必须通过 Object.defineProperty 实现响应式**

  ```JavaScript
  addName(state,name){
      // 必须通过 Object.defineProperty 实现响应式
      if (!state.name){
          // 实例对象 .$xxx
          // 构造函数 .xxx
          Vue.set(state,'name',name)
      }else{
          state['name'] = name // 踩坑，插入不成功
      }
  
  }
  ```

## 4 购物车小球数量整合

1. 先下载vuex ：npm install vuex --save 

2. 第二步：

   在main文件中引入使用

   ```JavaScript
   import store from './store/store'
   
   //在实例中挂载
   new Vue({
     el: '#app',
     router,
     store,
     components: { App },
     template: '<App/>'
   })
   ```

3. 建文件 store.js 

   ```javascript
   import Vue from 'vue'
   import Vuex from 'vuex'
   import num from './num'
   
   Vue.use(Vuex)
   
   let store = Vuex.Store({
     num:num
   })
   
   export default store
   ```

4. num.js

   ```javascript
   export default {
     state:{
       num:0
     },
     getter:{
       //...
     },
     mutations:{
       addNum(state,num){
         state.num += num
       }
     },
     // action中的异步行为
     actions:{
       addNumByAct({commit},num){
         commit('addNum',num)
       }
     }
   }
   ```

5. 使用。。如上

6. 使用：this.$store.state.变量名  就可以获取 与赋值 

7. 使用：this.$store.state.变量名  就可以获取 与赋值 









