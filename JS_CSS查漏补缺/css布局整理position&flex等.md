[TOC]



## 1 布局的基础+基本的PC端布局+移动端适配 

### 1.1 定位

定位：相对于其父元素、相对于浏览器窗口、浮动等

#### **position属性：**

static、relative、absolute、fixed、sticky和inherit 

- static(默认)：元素框正常生成。块级元素生成一个矩形框，作为文档流的一部分；行内元素则会创建一个或多个行框，置于其父元素中 
- relative：元素框相对于之前正常文档流中的位置发生偏移，并且<u>原先的位置仍然被占据。</u>发生偏移的时候，可能会覆盖其他元素。
- absolute：元素框不再占有文档流位置，并且相对于包含块进行偏移(所谓的<u>包含块就是最近一级position不为static的祖先元素</u>)
- fixed：元素框不再占有文档流位置，并且<u>相对于视窗</u>进行定位
- sticky：(css3+)粘性定位。相当于relative和fixed混合。最初会被当作是relative，相对于原来的位置进行偏移；一旦超过一定阈值之后，会被当成fixed定位，相对于视口进行定位。 

#### **偏移量：**top、right、bottom、left 

- 不同于margin：偏移量不会影响static元素起作用，但是margin对应盒子模型的外边距，会对每个元素框起到作用，使元素框与其他元素之间产生空白

#### **尺寸px/em/rem：**

- px ：像素（Pixel）。相对长度单位。像素px是相对于显示器屏幕分辨率而言的。IE无法调整那些使用px作为单位的字体大小；  Firefox能够调整px和em，rem 
- 百分比：百分比的参照物是父元素，50%相当于父元素width的50%
- :question: （往下看1.3）rem：这个对于复杂的设计图相当有用，它<u>是html的font-size的大小</u>。与em区别在于使用rem为元素设定字体大小时，仍然是相对大小，但相对的只是HTML根元素。 
- em：它虽然也是一个相对的单位，<u>相对于父元素的font-size</u>，但是，并不常用，主要是计算太麻烦了。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸16px 。 

#### **盒子模型：**

四部分：margin(外边距)+border(边框)+padding(内边距)+content(内容) 

css中存在**两种不同的盒子模型**，可以<u>通过box-sizing</u>设置不同的模型。两种盒子模型，主要是<u>width的宽度不同</u>。 

- 标准盒模型：width的长度等于content的宽度； 

  ![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\标准盒模型.jpg)

- 将box-sizing的属性值设置成border-box时，盒子模型的width=border+padding+content的总和。 

- 对于不同的模型的宽度是不同的。

- - 宽度默认的属性值是auto，这个属性值会使得内部元素的长度自动填充满父元素的width。 

  - 但是，height的属性值也是默认的auto，与width不同

    > :warning:auto这个属性值表示的是浏览器自动计算。这种自动计算，需要一个基准，一般浏览器都是允许高度滚动的，所以，会导致一个问题——浏览器找不到垂直方向上的基准。 
    >
    > :heavy_exclamation_mark:同样地道理也会被应用在margin属性上。 如果是块级元素水平居中只要将水平方向上的margin设置成auto就可以了。但是，垂直方向上却没有这么简单，因为你设置成auto时，margin为0。 

#### **浮动：**

最初设计是为了实现文字环绕的特效 。浮动的元素会在浮动层上面进行排布，而在原先文档流中的元素位置，会被以某种方式进行删除，但是还是会影响布局。 

浮动为什么会被使用在布局中呢？因为**设置浮动后的元素会形成BFC**（使得内部的元素不会被外部所干扰），并且元素的宽度也不再自适应父元素宽度，而是适应自身内容。这样就可以，轻松地实现多栏布局的效果。 

JavaScript可以改变CSS的属性，其他些属性还好，但是这个`float`值得一说，为何呢，因为float貌似是JavaScript中的一个关键字 

IE浏览器：

```JavaScript 
obj.style.styleFloat = "left";
```

其他浏览器：

```JavaScript 
obj.style.cssFloat = "left";
```

#### **清除浮动：**

浮动未做清除，容易造成高度塌陷的问题，清除浮动最常用方式：

- overflow: 将父元素的overflow，设置成hidden。
- :question:after伪类：对子元素的after伪类进行设置。

### 1.2 布局

#### **最初的布局——table**

table可以展示出多个块的排布。 现在的网页变得越来越复杂，适配的问题也是越来越多，这种布局不常用了。 

#### **两栏布局：**

一栏定宽，一栏自适应。这样子做的好处是定宽的那一栏可以做广告，自适应的可以作为内容主体。 

- 实现方式：
- 1. float + margin ：定宽，自适应
  2. position的absolute，可以使用同样的效果 

#### **三栏布局：**

两边定宽，然后中间的width是auto的，可以自适应内容，再加上margin边距，来进行设定。 

- 实现方式：

- 1. 使用左右两栏使用float属性，中间栏使用margin属性进行撑开，注意的是html的结果（缺点是：1. 当宽度小于左右两边宽度之和时，右侧栏会被挤下去；2. html的结构不正确 ）

     ```css
     .left{
       width: 200px;height: 300px; background: yellow; float: left;    
     }
     .right{
       width: 150px; height: 300px; background: green; float: right;
     }
     .middle{
       height: 300px; background: red; margin-left: 220px; margin-right: 160px;
     }
     ```

  2. 使用position定位实现：即左右两栏使用position进行定位，中间栏使用margin进行定位 （好处是：html结构正常。<u>缺点时：当父元素有内外边距时，会导致中间栏的位置出现偏差</u>）

  3. 使用float和BFC配合圣杯布局 ：将middle的宽度设置为100%，然后将其float设置为left，其中的main块设置margin属性，然后左边栏设置float为left，之后设置margin为-100%，右栏也设置为float：left，之后margin-left为自身大小。 （缺点是：1. 结构不正确 2. 多了一层标签）

  4. **flex布局** 

     1. ```css
        .wrapper{
            display: flex;
        }
        .left{
            width: 200px;
            height: 300px;
            background: green;
        }
        .middle{
            width: 100%;
            background: red;
            marign: 0 20px;
        }
        .right{
            width: 200px;
            height: 3000px;
            background: yellow;
        }
        ```

     

### 1.3 移动端时代

现在要求的网页都是需要能够去适配PC和移动端的。 媒体查询的主要原理：它像是给整个css样式设置了断点，通过给定的条件去判断，在不同的条件下，显示不同的样式。 

#### **媒体查询：**媒体查询的css标识符是@media 

需要一张网页能够在PC和移动端都能按照不同的设计稿显示出来，那么你需要做的就是去设置媒体查询。 媒体类型如下：

1. all， 所有媒体

2. braille 盲文触觉设备

3. embossed 盲文打印机

4. print 手持设备

5. projection 打印预览

6. screen 彩屏设备

7. speech ‘听觉’类似的媒体类型

8. tty 不适用像素的设备

9. tv 电视

   ```css
   @media only screen and (min-device-width : 320px) and (max-device-width : 480px) {
     /* Styles */
   }  /*使用例子*/
   ```

   

**flex弹性盒子：**

**rem适配：**rem可以说是移动端适配的一个神器。类似于手淘等界面，都是使用的rem进行的适配。这种界面有个特点就是页面<u>元素的复杂度比较高</u>，而<u>使用flex进行布局会导致页面被拉伸，但是上下的高度却没有变化等问题。</u> 



## 2.:star::star:flex（Flexible Box  ）弹性布局

布局的传统解决方案，基于[盒状模型](https://developer.mozilla.org/en-US/docs/Web/CSS/box_model)，依赖 [`display`](https://developer.mozilla.org/en-US/docs/Web/CSS/display) 属性 + [`position`](https://developer.mozilla.org/en-US/docs/Web/CSS/position)属性 + [`float`](https://developer.mozilla.org/en-US/docs/Web/CSS/float)属性。它对于那些特殊布局非常不方便，比如，[垂直居中](https://css-tricks.com/centering-css-complete-guide/)就不容易实现。 

2009年，W3C 提出了一种新的方案----Flex 布局，可以简便、完整、响应式地实现各种页面布局。 

Flex 布局将成为未来布局的首选方案。本文介绍它的语法，[下一篇文章](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)给出常见布局的 Flex 写法。网友 [JailBreak](http://vgee.cn/) 为本文的所有示例制作了 [Demo](http://static.vgee.cn/static/index.html)，也可以参考。

以下内容主要参考了下面两篇文章：[A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) 和 [A Visual Guide to CSS3 Flexbox Properties](https://scotch.io/tutorials/a-visual-guide-to-css3-flexbox-properties)。

### 2.1 什么是flex布局

任何一个容器都可以指定为 Flex 布局 

```css 
.box{
  display: flex;
}
/*行内元素也可以使用 Flex 布局。*/
.box{
  display: inline-flex;
}
/*Webkit 内核的浏览器，必须加上-webkit前缀*/
.box{
  display: -webkit-flex; /* Safari */
  display: flex;
}
```

:warning:**注意，设为 Flex 布局以后，子元素的`float`、`clear`和`vertical-align`属性将失效。** :warning:

### 2.2 基本概念

采用 Flex 布局的元素，称为 <u>Flex 容器</u>（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 <u>Flex 项目</u>（flex item），简称"项目"。 

![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\flex.png)

容器默认存在**两根轴**：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`。 

项目默认沿主轴排列。单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`。 

### 2.3 【容器】的属性6个

- **1、flex-direction**

  - 决定主轴的方向（即项目的排列方向） 

    ```css 
    .box {
      flex-direction: row | row-reverse | column | column-reverse;
    }
    row（默认值）：主轴为水平方向，起点在左端。
    row-reverse：主轴为水平方向，起点在右端。
    column：主轴为垂直方向，起点在上沿。
    column-reverse：主轴为垂直方向，起点在下沿。
    ```

- - ![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\flex-direction.png)

- **2、flex-wrap**

- - 默认情况下，项目都排在一条线（又称"轴线"）上。`flex-wrap`属性定义，如果一条轴线排不下，<u>如何换行</u>。 

    ```css 
    .box{
      flex-wrap: nowrap | wrap | wrap-reverse;
    }
    nowrap（默认）：不换行。
    wrap：换行，第一行在上方。
    wrap-reverse：换行，第一行在下方。
    ```

- - ![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\wrap1.png)

- - ![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\wrap2.jpg)

- - ![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\wrap3.jpg)

- **3、flex-flow**

- - `flex-flow`属性是`flex-direction`属性和`flex-wrap`属性的简写形式，默认值为`row nowrap`。 

  - ```css 
    .box {
      flex-flow: <flex-direction> || <flex-wrap>;
    }
    ```

- **4、justify-content**

- - `justify-content`属性定义了项目在主轴上的对齐方式。 

  - ```css  
    .box {
      justify-content: flex-start | flex-end | center | space-between | space-around;
    }
    /*具体对齐方式与轴的方向有关。下面假设主轴为从左到右。*/
    flex-start（默认值）：左对齐
    flex-end：右对齐
    center： 居中
    space-between：两端对齐，项目之间的间隔都相等。
    space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。
    ```

- - ![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\justify-content.png)

- **5、align-items**

- - 属性定义项目在交叉轴上如何对齐。 

  - ```css 
    .box {
      align-items: flex-start | flex-end | center | baseline | stretch;
    }
    /*具体的对齐方式与交叉轴的方向有关，下面假设交叉轴从上到下*/
    flex-start：交叉轴的起点对齐。
    flex-end：交叉轴的终点对齐。
    center：交叉轴的中点对齐。
    baseline: 项目的第一行文字的基线对齐。
    stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。
    ```

- - ![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\align-items.png)

- **6、align-content**

- - 定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。 

  - ```css  
    .box {
      align-content: flex-start | flex-end | center | space-between | space-around | stretch;
    }
    flex-start：与交叉轴的起点对齐。
    flex-end：与交叉轴的终点对齐。
    center：与交叉轴的中点对齐。
    space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
    space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
    stretch（默认值）：轴线占满整个交叉轴。
    ```

- - ![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\align-content.png)

### 2.4 【项目】的属性6个

- **1、order**

- - 定义项目的<u>排列顺序</u>。数值越小，排列越靠前，默认为0 

- - ```css 
    .item {
      order: <integer>;
    }
    ```

- - ![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\order.png)

- **2、`flex-grow`**

- - 定义项目的放大比例，**默认为`0`**，即如果存在剩余空间，也不放大（:question:只有width放大？？）。 

- - ```css 
    .item {
      flex-grow: <number>; /* default 0 */
    }
    /*如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。*/
    ```

- - ![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\flex-grow.png)

- **3、`flex-shrink`**

- - 定义了项目的缩小比例，**默认为1**，即<u>如果空间不足，该项目将缩小。</u> 负值对该属性无效。 

- - ```css 
    .item {
      flex-shrink: <number>; /* default 1 */
    }
    /*如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。*/
    ```

- - ![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\flex-shrink.jpg)

- **4、`flex-basis`**

- - 定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。 

- - ```css  
    .item {
      flex-basis: <length> | auto; /* default auto */
    }
    /*它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。*/
    ```

- **5、`flex`**

- - 是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。 

- - ```css  
    .item {
      flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
    }
    ```

- - 该属性有两个快捷值：`auto` (`1 1 auto`) 和 none (`0 0 auto`)。

    建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。

- **6、`align-self`**

- - 允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。 

  - ```css 
    .item {
      align-self: auto | flex-start | flex-end | center | baseline | stretch;
    }
    ```

- - ![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\align-self.png)

### :warning:还未看：Flex 布局教程：[实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)

## [3 rem（css3+）](http://www.cnblogs.com/lyzg/p/4877277.html)

与em区别在于使用rem为元素设定字体大小时，仍然是相对大小，但相对的只是HTML根元素。 

### 3.1 问题的引出

阅读白树的博文《[移动web资源整理](http://www.cnblogs.com/PeunZhang/p/3407453.html)》时，他在博文中有一段指出，如果html5要适应各种分辨率的移动设备，应该使用rem这样的尺寸单位 

在实际项目中，把与元素尺寸有关的css，如width,height,line-height,margin,padding等**都以rem作为单位**，这样页面在不同设备下就能保持一致的网页布局。 

实例：

- 顶部与底部的bar不管分辨率怎么变，它的高度和位置都不变
- 中间每条招聘信息不管分辨率怎么变，招聘公司的图标等信息都位于条目的左边，薪资都位于右边

这种app是一种典型的弹性布局：关键元素高宽和位置都不变，只有容器元素在做伸缩变换。对于这类app，**记住一个开发原则就好：文字流式，控件弹性，图片等比缩放。** 

font-size的设置：

```html 
<html data-dpr:"2" class="iphone6" style="font-size:xxxpx"></html>
```

这个deviceWidth通过document.documentElement.clientWidth就能取到了，所以当页面的dom ready后，做的第一件事情就是：

```javascript
document.documentElement.style.fontSize = document.documentElement.clientWidth / 6.4 + 'px';
```



## 4 viewport

只有明白了viewport的概念以及弄清楚了跟viewport有关的meta标签的使用，才能更好地让我们的网页适配或响应各种不同分辨率的移动设备。 

> META标签 是文档的最基本的元信息。 ，是在HTML网页源代码中一个重要的html标签。META标签用来描述一个HTML网页文档的属性，例如作者、日期和时间、网页描述、关键词、页面刷新等。 还涉及对关键词和网页等级的设定。 
>
> 所以有关搜索引擎注册、[搜索引擎优化](https://baike.baidu.com/item/%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%E4%BC%98%E5%8C%96)排名等网络营销方法内容中，通常都要谈论META标签的作用 ，合理利用 [Meta](https://baike.baidu.com/item/Meta) 标签的 [Description](https://baike.baidu.com/item/Description) 和[Keywords](https://baike.baidu.com/item/Keywords) 属性，加入网站的[关键字](https://baike.baidu.com/item/%E5%85%B3%E9%94%AE%E5%AD%97)或者网页的关键字，可使网站更加贴近用户体验。 

### 4.1 什么是viewport

通俗的讲，移动设备上的viewport就是设备的屏幕上能用来显示我们的网页的那一块区域，在具体一点，就是浏览器上(也可能是一个app中的webview)用来显示网页的那部分区域，但viewport又不局限于浏览器可视区域的大小，它可能比浏览器的可视区域要大，也可能比浏览器的可视区域要小。 

### 4.2 css中的1px并不等于设备的1px

桌面浏览器中css的1个像素往往都是对应着电脑屏幕的1个物理像素 

css中的像素只是一个抽象的单位，在不同的设备或不同的环境中，css中的1px所代表的设备物理像素是不同的。在为桌面浏览器设计的网页中，我们无需对这个津津计较，但在移动设备上，必须弄明白这点。 

1. 分辨率提高了一倍，变成640x960，但屏幕尺寸却没变化，这就意味着同样大小的屏幕上，像素却多了一倍，这时，一个css像素是等于两个物理像素的。 例如安卓设备根据屏幕像素密度可分为ldpi、mdpi、hdpi、xhdpi等不同的等级，分辨率也是五花八门，安卓设备上的一个css像素相当于多少个屏幕物理像素，也因设备的不同而不同，没有一个定论。 
2. 还有一个因素也会引起css中px的变化，那就是用户缩放。 

window对象有一个devicePixelRatio属性，它的官方的定义为：设备物理像素和设备独立像素的比例，也就是 **devicePixelRatio = 物理像素 / 独立像素**。 



### 4.3 PPK的关于三个viewport的理论

（[第一篇](http://www.quirksmode.org/mobile/viewports.html)，[第二篇](http://www.quirksmode.org/mobile/viewports2.html)，[第三篇](http://www.quirksmode.org/mobile/metaviewport/)） ppk认为，移动设备上有三个viewport 

css中的1px并不是代表屏幕上的1px，你分辨率越大，css中1px代表的物理像素就会越多，devicePixelRatio的值也越大，这很好理解，因为你分辨率增大了，但屏幕尺寸并没有变大多少，必须让css中的1px代表更多的物理像素，才能让1px的东西在屏幕上的大小与那些低分辨率的设备差不多，不然就会因为太小而看不清。 

ppk把移动设备上的viewport分为**\*layout viewport***  （document.documentElement.clientWidth ）、 **\*visual viewport***  （window.innerWidth  ） 和 **\*ideal viewport***  （ideal viewport并没有一个固定的尺寸，不同的设备拥有有不同的ideal viewport。所有的iphone的ideal viewport宽度都是320px，无论它的屏幕宽度是320还是640 ；安卓略复杂，参考[http://viewportsizes.com](http://viewportsizes.com/) ）三类，其中的ideal viewport是最适合移动设备的viewport，ideal viewport的宽度等于移动设备的屏幕宽度，只要在css中把某一元素的宽度设为ideal viewport的宽度(单位用px)，那么这个元素的宽度就是设备屏幕的宽度了，也就是宽度为100%的效果。ideal viewport 的意义在于，无论在何种分辨率的屏幕下，那些针对ideal viewport 而设计的网站，不需要用户手动缩放，也不需要出现横向滚动条，都可以完美的呈现给用户。 

### 4.4 利用meta标签对viewport进行控制 

移动设备默认的viewport是layout viewport，也就是那个比屏幕要宽的viewport，但在进行移动设备网站的开发时，我们需要的是ideal viewport。 

轮到**meta标签**出场了 

```HTML
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
<!--让当前viewport的宽度等于设备的宽度，同时不允许用户手动缩放。-->
```

在苹果的规范中，meta viewport 有6个属性(暂且把content中的那些东西称为一个个属性和值) 可以同时使用，也可以单独使用或混合使用，多个属性同时使用时用逗号隔开就行了。 

| width         | 设置**\*layout viewport***  的宽度，为一个正整数，或字符串"width-device" |
| ------------- | ------------------------------------------------------------ |
| initial-scale | 设置页面的初始缩放值，为一个数字，可以带小数                 |
| minimum-scale | 允许用户的最小缩放值，为一个数字，可以带小数                 |
| maximum-scale | 允许用户的最大缩放值，为一个数字，可以带小数                 |
| height        | 设置**\*layout viewport***  的高度，这个属性对我们并不重要，很少使用 |
| user-scalable | 是否允许用户进行缩放，值为"no"或"yes", no 代表不允许，yes代表允许 |

在安卓中还支持  target-densitydpi  这个私有属性，它表示目标设备的密度等级，作用是决定css中的1px代表多少物理像素

| target-densitydpi | 值可以为一个数值或 high-dpi 、 medium-dpi、 low-dpi、 device-dpi 这几个字符串中的一个( 当 target-densitydpi=device-dpi 时， css中的1px会等于物理像素中的1px ) |
| ----------------- | ------------------------------------------------------------ |
|                   |                                                              |

### 4.5 把当前的viewport宽度设置为 ideal viewport 的宽度

-  因为meta viewport中的width能控制layout viewport的宽度，所以我们只需要把width设为width-device这个特殊的值就行了。

```html 
<meta name="viewport" content="width=device-width">
<!--在iphone和ipad上，无论是竖屏还是横屏，宽度都是竖屏时ideal viewport的宽度。-->
```

```html 
<meta name="viewport" content="initial-scale=1">
<!--因为这里的缩放值是1，也就是没缩放，但却达到了 ideal viewport 的效果，所以，那答案就只有一个了，缩放是相对于 ideal viewport来进行缩放的-->
```

- 这句代码也能达到和前一句代码一样的效果，也可以把当前的的viewport变为 ideal viewport。 
- 如果width 和 initial-scale=1同时出现，并且还出现了冲突呢？ 

```HTML
<meta name="viewport" content="width=400, initial-scale=1">
<!--浏览器会取它们两个中较大的那个值。-->
```

- 最完美的写法应该是，两者都写上去，这样就 initial-scale=1 解决了 iphone、ipad的毛病，width=device-width则解决了IE的毛病：

  ```html 
  <meta name="viewport" content="width=device-width, initial-scale=1">
  ```



### 4.6 关于meta viewport的更多知识

 缩放是相对于ideal viewport来缩放的，缩放值越大，当前viewport的宽度就会越小，反之亦然 

如果我们设置 initial-scale=2 ， 原来1px的东西变成2px了，但是1px变为2px并不是把原来的320px变为640px了，而是在实际宽度不变的情况下，1px变得跟原来的2px的长度一样了，所以放大2倍后原来需要320px才能填满的宽度现在只需要160px就做到了。 

```
visual viewport宽度 = ideal viewport宽度  / 当前缩放值

当前缩放值 = ideal viewport宽度  / visual viewport宽度
ps: visual viewport的宽度指的是浏览器可视区域的宽度。
```

*但是安卓上的原生浏览器以及IE有些问题。 安卓自带的webkit浏览器只有在 initial-scale = 1 以及没有设置width属性时才是表现正常的，也就相当于这理论在它身上基本没用；而IE则根本不甩initial-scale这个属性，无论你给他设置什么，initial-scale表现出来的效果永远是1。* 

**在iphone和ipad上，无论你给viewport设的宽的是多少，如果没有指定默认的缩放值，则iphone和ipad会自动计算这个缩放值，以达到当前页面不会出现横向滚动条(或者说viewport的宽度就是屏幕的宽度)的目的。** 

**动态改变meta viewport标签** 

1. 可以使用document.write来动态输出meta viewport标签，例如：

   ```javascript
   document.write('<meta name="viewport" content="width=device-width,initial-scale=1">')
   ```

2. 通过setAttribute来改变 

   ```html 
   <meta id="testViewport" name="viewport" content="width = 380">
   <script>
   var mvp = document.getElementById('testViewport');
   mvp.setAttribute('content','width=480');
   </script>
   ```

**总结**

- 如果不设置meta viewport标签，那么移动设备上浏览器默认的宽度值为800px，980px，1024px等这些，总之是大于屏幕宽度的。这里的宽度所用的单位px都是指css中的px，它跟代表实际屏幕物理像素的px不是一回事。 

- 每个移动设备浏览器中都有一个理想的宽度，这个理想的宽度是指css中的宽度，跟设备的物理宽度没有关系 在css中，这个宽度就相当于100%的所代表的那个宽度。我们可以用meta标签把viewport的宽度设为那个理想的宽度，如果不知道这个设备的理想宽度是多少，那么用device-width这个特殊值就行了，同时initial-scale=1也有把viewport的宽度设为理想宽度的作用。所以，我们可以使用

  ```HTML 
  <meta name="viewport" content="width=device-width, initial-scale=1">
  ```

