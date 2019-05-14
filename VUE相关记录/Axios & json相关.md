[TOC]

# Axios & json相关

## 1 vue之axios请求本地json

> 1.如何引入axios，import、prototype
>   本地JSON文件需放在static文件夹之下。（以及图片文件）。参见http://blog.csdn.net/Mr_YanYan/article/details/78783091
> 2.response是个Object对象，但是response.data才是本地JSON文件的对象
> 3.response.data已经是一个Object类型；原生JS写Ajax返回的response是string类型
> 4.JSON文件不得有注释，否则返回的是string
> 5.JSON文件如果有注释，JSON.parse报错含有非法字符/。JSON文件本来就是对象，再用。
>
> 6.JSON.parse(),会报错含有非法字符o

写给自己的话：**静态的json文件要记得放在static文件夹下**

**1.下载插件**

   npm install axios --save

**2.在main.js下引用axios**

  import axios from 'axios'

  Vue.prototype.$http=axios

**3.在static文件夹下写静态文件 home.json**

```JavaScript
{
   "name":"xuexue",
   "age":20
}
```

**4.在组件中请求数据**

```JavaScript
this.$http.get('../../static/home.json')
.then(function(res){
  console.log(res)
}
.catch(err=>{console.log(err)})
)
```

## 2 json中数据的格式

标签和内容都要用双引号“”括起来

```json
{
  "status": 0,
  "message": [
    {"srr":"../../static/img/lunBo/1.png","alt":"page1"},
    {"srr":"../../static/img/lunBo/2.png","alt":"page2"},
    {"srr":"../../static/img/lunBo/3.png","alt":"page3"},
    {"srr":"../../static/img/lunBo/4.png","alt":"page4"},
    {"srr":"../../static/img/lunBo/5.png","alt":"page5"},
    {"srr":"../../static/img/lunBo/6.png","alt":"page6"},
    {"srr":"../../static/img/lunBo/7.png","alt":"page7"}
  ]
}
```

## 3 json中含有html代码的处理

给一个v-html属性，值为插入的内容

```HTML
<div class="news-content" v-html="newsInfo.content"></div>
```



## 4 $axios.all([promise1,promise2]).then(传播响应).catch()

请求的不同URL内容，要么都出现，要么都不出现， 

```JavaScript
created() {
    //商品轮播图 getthumimages/:imgid
  //商品信息 goods/getinfo/:goodsid
  //请求一个失败了就算失败，可以通过 $axios.all([promise1,promise2]).then(传播响应).catch()
  let imgReq = this.$axios.get(`getlunbo.json`)
  let infoReq = this.$axios.get(`goods/getgoods1.json`)
  // console.log(imgReq) //是Promise对象

  //发请求，这样做是为了要么都出现，要么都不出现！！！
  this.$axios.all([imgReq,infoReq])
    .then(this.$axios.spread(
      (imgRes,infoRes)=>{
        this.imgs = imgRes.data.message
        this.info = infoRes.data.message[this.goodsId-1]
        // console.log(this.info)
      })
    ).catch(console.log)
}
```



## 5 json & XML 

- **什么是json协议**

JSON协议事实上已经作为一种前端与服务器端的**数据交换格式**，是一种国际标准。他不是语言，他只是一个规范，按照这种规范写法就能实现数据传递

- **json和json对象的区别**
- - ajax，后台一般传递给我们的数据格式是json字符串，我们拿到数据之后，将其转化成json对象，再做其他处理
- - **JSON.stringify(obj)**将JSON转为字符串
- - 通过**eval()** 函数可以将JSON字符串转化为对象。
- - **JSON.parse(string)**将字符串转为JSON对象
- **xml协议和json协议的优缺点比较**

json是一种网络通用协议

更为通用的xml协议

Xml只是描述数据的一种结构，比如大家常用的html就是采用这种结构描述的。

XML的重要性之最 –统一通信协议c

Json – 特殊的xml

前后台沟通的桥梁xml json

既可以用json 也可以用xml

Web前端开发 json更流行

相同点：

都是一种通用协议

都可以用来描述数据

不同点：

JSON相对于XML来讲，数据的体积小，传递的速度更快些。

xml专用带宽大，json占用带宽小

json没有xml这么通用

json可以和js对象互相转换，和js是天生的一对，因此广泛用于前端开发

XML已经被业界广泛的使用，而JSON才刚刚开始，但是在Ajax这个特定的领域，未来的发展一定是XML让位于JSON。到时Ajax应该变成Ajaj(Asynchronous Javascript and JSON)了