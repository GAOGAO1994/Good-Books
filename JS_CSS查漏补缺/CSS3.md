[TOC]

# CSS3积累

## 1 动画相关

### 1.1 识别码-前缀

这种方式在业界上统称：**识别码、前缀**

//-ms-	代表【ie】内核识别码

//-moz-	代表火狐【firefox】内核识别码

//-webkit-	代表谷歌【chrome】/苹果【safari】内核识别码

//-o-	代表欧朋【opera】内核识别码

> **为什么要加识别码：**
>
> 在标准还未确定时，部分浏览器已经根据最初草案实现了部分功能，为了与之后确定下来的标准进行兼容，所以每种浏览器使用了自己的私有前缀与标准进行区分，当标准确立后，各大浏览器将逐步支持不带前缀的css3新属性。
>
> 目前已有很多私有前缀可以不写了，但为了兼容老版本的浏览器，可以仍沿用私有前缀和标准方法，逐渐过渡。

> **再进一步看前缀对应的内核：**
>
> 上面说了-webkit对应的是谷歌/苹果的内核，这样的说法是不够具体的，下面是更正后的说法：
>
> Gecko内核,css前缀为"-moz-",火狐浏览器
>
> WebKit内核,css前缀为"-webkit-",Comodo Drangon(科摩多龙)，苹果，安卓，搜狗高速浏览器3，快快浏览器，枫树浏览器，云游浏览器，360极速浏览器，世界之窗极速版，SRWare Iron，猎豹浏览器，RockMelt，QQ浏览器
>
> Blink内核,css前缀为"-webkit-",Blink是一个由Google和Opera Software开发的浏览器排版引擎，Google的新内核，支持以前的全部前缀

### 1.2 transition 属性

**注意：** 始终指定transition-duration属性，否则持续时间为0，transition不会有任何效果。

| 默认值：          | all 0 ease 0                         |
| :---------------- | ------------------------------------ |
| 继承：            | no                                   |
| 版本：            | CSS3                                 |
| JavaScript 语法： | *object*.style.transition="width 2s" |

```
transition: property duration timing-function delay;
```

| 值                                                           | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [transition-property](http://www.w3school.com.cn/cssref/pr_transition-property.asp) | 规定设置过渡效果的 CSS 属性的名称。**只有能数字量化的CSS属性才能过渡。**如`width，height，top，right，bottom，left，zoom，opacity，line-height，background-position，word-spacing，font-weight，vertical-align，outline-outset，z-index`等。 |
| [transition-duration](http://www.w3school.com.cn/cssref/pr_transition-duration.asp) | 规定完成过渡效果需要多少秒或毫秒。单位可指定s秒，也可指定ms毫秒。默认值是0，表示立刻变化。 |
| [transition-timing-function](http://www.w3school.com.cn/cssref/pr_transition-timing-function.asp) | 规定速度效果的速度曲线（过渡曲线）。*有`linear`，`ease`，`ease-in`，`ease-out`，`ease-in-out`，`cubic-bezier(n,n,n,n)`，`steps`。其实它们都是贝赛尔曲线。* |
| [transition-delay](http://www.w3school.com.cn/cssref/pr_transition-delay.asp) | 定义过渡效果何时开始。                                       |

![img](https://upload-images.jianshu.io/upload_images/1959053-ec85b070d87e5608.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/561/format/webp)

上图：贝塞尔曲线

> 如果预定义好的这些函数满足不了你的需求，可以通过`cubic-bezier(n,n,n,n)`自定义平滑曲线。从上面的图形中观察到，贝塞尔曲线有4个点，左下为起始点P0坐标固定为(0,0)，右上为终点P3坐标固定为(1,1)，中间有两点P1和P2的坐标就是`cubic-bezier(n,n,n,n)`的参数。通过4条连起来的直线，生成平滑的曲线。一图胜千言：[详见戳](https://www.jianshu.com/p/56f8ddafc63f)

```css
#tempDiv {
    padding: 1px;
    transition-property: padding;
    transition-duration: 1s;
}
#tempDiv:hover {
    padding: 5px;
}
```

#### 隐式过渡

transition过渡时有时会出现一些比较暧昧的情形，比如设成em的属性，如你所知em是根据font-size来计算的。类似还有rem，vh，vw等都是根据另一个属性的值来计算得到它的值。举个例子`padding:2em;`，如果font-size被改变了，此时padding的“书面值”不变，仍旧是2em，但“实际值”将会发生变化并触发transition过渡。这被称作“隐式过渡”。多数浏览器会实现隐式过渡，但传闻IE很特别

#### 开关过渡和永久过渡

开关过渡，顾名思义就是触发源的事件结束后会恢复到原始状态。永久过渡就是过渡后不恢复到原始状态。

```css
//永久过渡
.forever { 
    transition: all 1s ease-in-out 999999s;
}/*transition-duration被设成了一个很大的时间，999999s差不多有12天，理论上你页面开12天就能看到关闭过渡的效果，但现实等于永久过渡。*/
.forever:hover { 
    transform: scale(1.5);
    transition: all 1s ease-in-out;
}
```

### 1.3 transform介绍

transform本质上是一系列变形函数，分别是translate位移，scale缩放，rotate旋转，skew扭曲，matrix矩阵。[具体请戳](https://www.jianshu.com/p/17e289fcf467)

**前置属性：**transform-origin（指定元素变形的中心点）、transform-style（两个值`flat`和`preserve-3d`。用于指定舞台为2D或3D）、perspective（指定3D的视距，借用rotateX / rotateY / rotateZ来明确xyz轴坐标的基本概念）、perspective-origin（设置视距的基点）、backface-visibility（是否可以看见3D舞台背面）、

**2D变形：**translate、scale、rotate、skew、matrix

**3D变形：**translate3d、scale3d、rotate3d、matrix3d、

**层级影响**