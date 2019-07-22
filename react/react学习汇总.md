react

## what-why-how

**什么是react：**一个创建用户接口的JavaScript库，react项目运行在浏览器中，这带来了很大的优势，我们不需要等待页面响应加载

组件化思想，我们不需要将整个网页写成一整个大图，可以用组件的形式完成。书写可维护的、便于管理的、可复用的分片。



**JSX code**

ReactDOM.render() 方法将JavaScript函数变成一个真实的DOM组件

**为什么用react：**

- 单页应用：只有一个HTML页面，内容被渲染在客户端；pages以组件的形式存在，内个组件就是一个react组件；整个页面由一个根react组件进行管理控制
- 多页应用：多个HTML页面，内容在服务器；整个页面不受react控制，小部件之间不知道对方的存在



### react历史

- vue双向数据绑定 -> 双向数据流
- react单向数据流 
  - 内存的改变影响页面的改变，不管页面的改变，影响内存的改变
  - 自己处理页面的改变，影响内存：通过事件，调用函数，通知内存对象改变页面
- 没有指令

### react双向绑定



### react生命周期

**render的生命周期**

1. `render()`

   - 在一个 class 或者 function 组件中必须需要的，**可以做渲染过滤**

     `render() will not be invoked（调用） if shouldComponentUpdate() returns false.`

   :star::star:：灵活运用解构赋值！！！

   ```JavaScript
   // 声明一个age、name属性，this.props中的同名属性进行赋值
   let { name, age } = this.state;
   let { name, age } = this.props;
   
   // 声明一个引用类型属性，this.props中同名对象的地址进行赋值
   // 改变obj.xxx与props有关
   let { obj } = this.props;
   ```

   

**constructor()**

1. `constructor(props)`

   react 的构造器是在装载之前被调用的，当构造器实现了`React.Component`类，你应该在任意的生命周期之前调用`super(props)`

   `Otherwise, this.props will be undefined in the constructor, which can lead to bugs.`

   常见的构造函数内目的

   - `Initializing local state by assigning an object to this.state.`通过将对象分配给this.state来初始化本地状态。

   - `Binding event handler mehods to an instance`将事件处理方法绑定到实例

     ```react
     constructor() {
             super();
             this.state = {
                 num: 2,
             };
         //    初始化函数的指向，改变changeHandler的this的上下文
             this.changeHandler = this.changeHandler.bind(this);
         }
     ```

**componentDidMount()，相当于mounted（vue）**

- 数据已经装载，据说会引起二次render，**实际应用：发网络请求**

- 组件安装后立即调用`componentDidMount()`（插入到树中）。需要DOM节点的初始化应该到这里。如果需要远程加载数据，那么这是实例化网络请求的好方法。【此方法是建立任何订阅的好地方，但不要忘记在`componentWillUnmount()`中取消订阅。】

**componentDidUpdate()**

- `componentDidUpdate(prevProp之前的属性, prevState之前的数据, snapshot)`

  `componentDidUpdate() is invoked immediately after updating occurs. This method is not called for the initial render.`更新发生后立即调用componentDidUpdate（）。 初始渲染不会调用此方法。

- 当组件更新时，将此作为在DOM上操作的机会。这也是一个进行网络请求的好地方，只要当前的props与以前的props进行比较（例如，如果props没有改变，网络请求可能就不需要了）。

  ```react
  componentDidUpdate(prevProps) {
      // 不要忘记比较 props
      if (this.props.userID !== prevProps.userID) {
          this.fetchData(this.props.userID);
      }
  }
  // 如果shouldComponentUpdate() 返回false，那么此方法不会被调用
  ```

**componentWillUnmount()**

- 在constructor之后触发，官方不推荐在此处发请求，原因可能会造成渲染的阻塞

**顺序：**`constructor => componentWillMount => render => componentDidMount`

### 父子组件通信

**父 -> 子传参**

1. 写子组件：Son.js，要export出去！！
2. 父组件中 import 子组件，并在render return中使用`<Son name={xxx} />`
3. 子组件中通过 `this.props.name`进行调用	

**父子组件传组件**

1. 父组件引用子组件时，使用：`<Son> <ul>...</ul> </Son>`
2. 子组件通过：`this.props.children`接收

**:warning::star:实现类似`vue`中`slot`的功能**

- 将 部分组件用父子传参的方式写入
- 子组件中 通过`this.props.xxx`的方式调用

### 添加样式

- **内联样式：**`<div style={ {background: 'green'} }>`



### 属性的约定和默认值 —— propTypes

- 需要引入 prop-types ：`import PropTypes from 'prop-types';`

- 在class中定义静态属性，

  ```react
  class Son extends Component {
      // 属性的约定和默认值
      static propTypes = {
          text: PropTypes.string.isRequired  
          // ||string      // .isRequired
          // 注意！！！因为此处设置为 string 类型
          // 所以如果父组件中 text 设为 number 会报错
      };
  
      static defaultProps = {
          text: 'abc'
      };
  	...
      render() {
          let {name, age, text} = this.props;
          console.log(this.props.children);
          return (
              <div>
                  text:{text}
              </div>
          )
      }
  }
  ```

### 内置便捷函数

- `this.props.childern`有可能有3种数据结构：对象/数组/undefined
- `React.Children.map(children, function[(thisArg)])` // map函数返回数组
- **目的：**更加健壮的排除空节点`undefined`或者 单节点`Object`或 多节点`Array`，开发时尽可能这样使用来操作数组
- `React.Children.forEach(children, function[(thisArg)])` // 遍历

### React 16.+版本新功能，作为了解即可

```react
render () {
    ...
	return (
        // 可以通过数组的方式，来管理div
        [<span>{num}</span>, <hr/>]
    )
}
```

### React.Fragment:star:

- 用来解决多余的div根节点，不会生成DOM元素

```react
render () {
	...
	return (
        <React.Fragment>
            <span>{num}</span>
            <hr/>
            { React.Children.map(children, child => child)}
        </React.Fragment>
    )
}
```



### react 15 和 16 的区别

`React.createClass`和`extends Component`的区别主要在于：

- 语法区别
- propType 和 getDefaultProps
- 状态的区别
- this的区别
- Mixins混合的区别

#### 1、语法区别

`React.createClass`

```react
import React from 'react';

const Contacts = React.createClass({
    render () {
        return {
            <div></div>
        }
    }
});
export default Contacts;
```

`React.Component`

```react
import React from 'react';

class Contacts extends React.Component {
    constructor(props) {
        super(props);
    }
    render(){
        return (
            <div></div>
        )
    }
}
export default Contacts;
```

#### 2、propType 和 getDefaultProps

**`React.createClass`**通过`proTypes`对象和`getDefaultProps()`方法来设置和获取 `props`

**`React.Component`**通过设置两个属性`propTypes`和`defaultProps`

```react
class Son extends Component {
    // 属性的约定和默认值
    static propTypes = {
        text: PropTypes.string.isRequired  
        // 注意！！！因为此处设置为string类型，所以如果text赋值为number会报错
    };
// 如果父组件没有传递相应的 text 参数，那么默认为abc
    static defaultProps = {
        text: 'abc'
    };

    constructor(props) {
        super(props);
    }

    render() {
        return <div></div>
    }
}
```

#### 3、状态的区别

**`React.createClass`**通过`getInitialState()`方法返回一个包含初始值的对象

```react
import React from 'react';
let TodoItem = React.createClass({
    // return an object
    getInitialState(){
        return {
            isEditing: false
        }
    },
    render(){
        return <div></div>
    }
})
```

**`React.Component`**通过`constructor`设置初始状态

```react
import React from 'react';
class TodoItem extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            isEditing: false
        }
    }
    render(){
        return <div></div>
    }
}
```

#### 4、this 的区别

**`React.createClass`**会正确绑定 this

```react
import React from 'react';
const Contacts = React.createClass({
    handleClick(){
        console.log(this); // 指向React组件的实例
    },
    render(){
        return(
            // 会切换到正确的this上下文
            <div onClick={this.handleClick}></div>
        );
    }
});
export default Contacts;
```

**`React.Component`**由于使用了ES6，属性不会自动绑定到React类的实例实例上。

```react
class TodoItem extends React.Component{
    constructor(props){
        super(props);
    }
    handleClick(){
        console.log(this); // null
    }
    handleFocus(){	// manually bind this
        console.log(this);  // React Component Instance
    }
    handleBlur: () => {	// use arrow function
        console.log(this);	// React Component Instance
    }
    
    render(){
        return <input onClick={this.handleClick}
        			onFocus={this.handleFocus.bind(this)}
    				onBlur={ e => this.handleBlur() } />
    }
}
```

:star::star:还可以在constructor中来改变`this.handleClick`执行的上下文，这应该是相对上面一种来说更好的方法，如果要改变语法结构，完全不需要改动JSX的部分：

```react
class Contacts extends React.Component{
    constructor(props){
        super(props);
        this.handleClick = this.handleClick.bind(this);
    }
    handleClick(){
        console.log(this); // React Component instance
    }
    
    render(){
        return <input onClick={this.handleClick} />
    }
}
```

#### 5、Mixins（混合/继承操作）

如果使用ES6的方式来创建组件，就不能使用`React mixins`的特性了。

**`React.createClass`**使用此方法，可以在创建组件的时候添加一个叫mixins的属性，将可供混合的类的集合以数组的形式赋给mixins。

```react
let MyMixin = {
    doSomething(){}
}
let TodoItem = React.createClass({
    mixins: [MyMixin], // add mixin
    render(){
        return <div></div>
    }
})
```

**`React.Component`**

```react
export default function(Son) {
    return class wrap extends Component {
   		componentDidMount(){
    	    console.log('aaaa');
    	    console.log();
    	}
   		render(){
   	    	<Son />
   		}
    }
}
```

作为函数接收一个组件，render那个组件，在js部分先执行，拿到组件对象<组件 />使用。

高阶函数，函数接收组件，render另一个组件，从而添加一些功能。

### 获取DOM元素

- 在组件上使用 `ref="xxx"`
- 在组件中使用`this.refs.xxx`获取



### 插件相关

### react 路由

- `npm i react-router-dom` 在浏览器使用
- `react-router-native`开发手机原生应用时使用
- 推荐使用`react-navigation`

#### **使用流程**

- 下载`npm i react-router-dom`
- 引入对象：按需引入：HashRouter，BrowserRouter(无#，推荐使用)，Route
- HashRouter：代表路由框架的坑
- BrowserRouter：路由的坑，最终没有#，原理基于：click+history.pushState
- Route：代表锚点和填入的位置

#### **Switch选择一个**

- 横向匹配一个，若指向同一个路由地址，只匹配第一个

- 被其包裹的Router从上到下，只会匹配一个（选其一）

- ```react
  <Switch>   
       {/*按顺序匹配*/}
      <Route path="/a/man" exact component={Man}/>    
      <Route path="/a/man" exact component={Woman}/> 
      <Route path="/a/woman" exact component={Woman}/>
  </Switch>
  ```

#### **exact精确匹配**

- :star:纵向（深入）匹配一个，不继续往里面走(孩子路由不匹配)

- 精确匹配锚点

- 应用场景：‘首页‘需要使用，因为/范围太大，会匹配很多东西

  - 其他路由不使用，为了能够使用嵌套路由（不要控制纵向（深入）只匹配一个）

- ```react
  <Route path="/" exact component={Home}/>
  ```

#### **默认重定向（相当于404）**

- 放在路由最后一位，用来处理页面找不到，配合Switch

- Redirect，to属性：不管输入什么地址，最终重定向到  "/"

  ```react
  <Redirect to="/" />
  ```

#### `NavLink类似router-link`

- NavLink 按需引入 `import {NavLink} from 'react-router-dom';`

- 当做组件来使用，其中属性to（路径，与Router中的path匹配）

- ```react 
  <Router>
      <React.Fragment>
          <NavLink to="/a/man" activeStyle={{color:'#4dc060'}}>点击后的样式</NavLink>
          <NavLink to="/a/woman" activeClassName="selected">点击后的样式</NavLink>
          <Route path="/" component={Home}/>
      </React.Fragment>
  </Router>
  ```

#### 代码注意

- 主体组件只能有一个根节点
- 主体坑（HashRouter）也只能有一个根节点
  - NavLink只能在主体坑中













