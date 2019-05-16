[TOC]

## css使用技巧

#### 1 文字的水平居中 

　text-align:center; 

#### 2. 容器的水平居中 

先为该容器设置一个明确宽度，然后将margin的水平值设为auto即可。 

#### 3. 文字的垂直居中 

单行文字的垂直居中，只要将行高与容器高设为相等即可。 

```css 
<div id="container">1234567890</div>  /*容器*/
div#container {height: 35px; line-height: 35px;}  /*css*/
```

#### 4.容器的垂直居中

有一大一小两个容器，请问如何将小容器垂直居中?

- 将大容器的定位为relative。 
- 将小容器定位为absolute，再将它的左上角沿y轴下移50%，最后将它margin-top上移本身高度的50%即可。 (因为top和bottom不能同时设置)

#### 5.图片宽度自适应

```css
img {max-width: 100%} /*自动适应小容器的宽度*/
img {width: 100%}  /*IE6不支持max-width，所以遇到IE6时，使用IE条件注释*/
```

#### 6.按钮3d效果

只要将它的左上部边框设为浅色，右下部边框设为深色即可 

background: #888; border: 1px solid; border-color: #999 #777 #777 #999; 

#### 7.font属性的快捷写法

font: font-style font-variant font-weight font-size line-height font-family; 

#### 8.link状态的设置顺序

 link的四种状态，需要按照下面的前后顺序进行设置： 

　a:link  　　a:visited  　　a:hover  　　a:active 

#### 9.IE条件注释

可以利用条件注释，设置只对IE产生作用的语句 

<!--[if IE]>  　　　　<link rel="stylesheet" type="text/css" href="ie-stylesheet.css" />  　　< ![endif]--> 

10.IE6专用语句：方法一

由于IE6不把html视为文档的根元素 

```css
* html body{
　　}
* html .foo{
　　}
/*IE7*/
*+html .foo{
　　}
```

方法二：除了IE6以外，所有浏览器都不能识别属性前的下划线。而除了IE7之外，所有浏览器都不能识别属性前的*号 

```css
.element { 
　　　　background: red; /* modern browsers */ 
　　　　*background: green; /* IE 7 and below */ 
　　　　_background: blue; /* IE6 exclusively */ 
　　}
```

#### 12.css的优先性

**行内样式 > id样式 > class样式 > 标签名样式** 

div < .class < div.class < #id < div#id < #id.class < div#id.class 

#### 13.IE6的min-height

IE6不支持min-height，有两种方法可以解决这个问题： 

```css 
/*方法一：*/
.element { 
　　　　min-height: 500px; /*针对其他浏览器设置最小高度*/
　　　　height: auto !important; /*让其他浏览器覆盖第三句的设置*/
　　　　height: 500px; /*针对IE设置最小高度*/
　　}
/*方法二*/
.element { 
　　　　min-height: 500px 
　　　　_height: 500px 
　　} /*_height只有IE6能读取*/
```

#### 14.font-size基准

浏览器的缺省字体大小是16px，你可以先将基准字体大小设为10px ；

后面统一采用em作为字体单位，2.4em就表示24px 

#### 15.Text-transform和Font Variant 

Text-transform用于将所有字母变成小写字母、大写字母或首字母大写 

```css
p {text-transform: uppercase}  
　　p {text-transform: lowercase} 
　　p {text-transform: capitalize}
```

Font Variant用于将字体变成小型的大写字母（即<u>与小写字母等高的大写字母</u>） 

```css
p {font-variant: small-caps}
```

#### 16.css重置

用于取消浏览器的内置样式，请参考[YUI](http://developer.yahoo.com/yui/reset/)和[Eric Meyer](http://meyerweb.com/eric/thoughts/2007/05/01/reset-reloaded/)的样式表。 

#### 17.用图片充当列表标志

浏览器使用一个黑圆圈作为列表标志，可以用图片取代它 

```css
　　ul {list-style: none}

　　ul li { 
　　　　background-image: url("path-to-your-image"); 
　　　　background-repeat: none; 
　　　　background-position: 0 0.5em; 
　　}    /*联系雪碧图*/
```

#### 18.将容器设为透明

```css 
.element { 
　　　　filter:alpha(opacity=50); /*IE专用*/
　　　　-moz-opacity:0.5; /*Firefox*/
　　　　-khtml-opacity: 0.5; /*webkit核心*/
　　　　opacity: 0.5; /*opera*/
　　}
```

#### 19.css三角形

先编写一个空元素 ，将它四个边框中的三个边框设为透明，剩下一个设为可见，就可以生成三角形效果 

```css
.triangle { 
　　　　border-color: transparent transparent green transparent; 
　　　　border-style: solid; 
　　　　border-width: 0px 300px 300px 300px; 
　　　　height: 0px; 
　　　　width: 0px; 
　　}
```

#### 20.禁止自动换行

```css
h1 { white-space:nowrap; }
```

#### 21.用图片替换文字

需要在标题栏中使用图片，但是又必须保证搜索引擎能够读到标题 

```css
h1 { 
　　　　text-indent:-9999px;  /*首行文字的缩进效果*/
　　　　background:url("h1-image.jpg") no-repeat; 
　　　　width:200px; 
　　　　height:50px; 
　　}
```

#### 22.获得焦点的表单元素

当一个表单元素获得焦点时，可以将其突出显示： 

```css
input:focus { border: 2px solid green; }
```

#### 23.!important规则

多条CSS语句互相冲突时，具有!important的语句将覆盖其他语句。 

由于IE不支持!important，所以也可以利用它区分不同的浏览器。 

#### 24. :question:css提示框

当鼠标移动到链接上方，会自动出现一个提示框。 

```css
<a class="tooltip" href="#">链接文字 <span>提示文字</span></a>

a.tooltip {position: relative} 
　　a.tooltip span {display:none; padding:5px; width:200px;} 
　　a:hover {background:#fff;} /*background-color is a must for IE6*/ 
　　a.tooltip:hover span{display:inline; position:absolute;}
```

#### 25.固定位置的首页

```css
body{ margin:0;padding:100px 0 0 0;}

　　div#header{
　　　　position:absolute;
　　　　top:0;
　　　　left:0;
　　　　width:100%;
　　　　height:<length>;
　　}

　　@media screen{
　　　　body>div#header{position: fixed;}
　　}

　　* html body{overflow:hidden;}

　　* html div#content{height:100%;overflow:auto;}
```

#### 26.在IE6中设置PNG图片的透明效果

```css 
.classname {
　　　　background: url(image.png);
　　　　_background: none;
　　_filter:progid:DXImageTransform.Microsoft.AlphaImageLoader
　　　　　　　　(src='image.png', sizingMethod='crop');
　　}
```

#### 27.各类浏览器的专用语句

```css
　　/* IE6 and below */
　　* html #uno { color: red }

　　/* IE7 */
　　*:first-child+html #dos { color: red } 

　　/* IE7, FF, Saf, Opera */
　　html>body #tres { color: red }

　　/* IE8, FF, Saf, Opera (Everything but IE 6,7) */
　　html>/**/body #cuatro { color: red }

　　/* Opera 9.27 and below, safari 2 */
　　html:first-child #cinco { color: red }

　　/* Safari 2-3 */
　　html[xmlns*=""] body:last-child #seis { color: red }

　　/* safari 3+, chrome 1+, opera9+, ff 3.5+ */
　　body:nth-of-type(1) #siete { color: red }

　　/* safari 3+, chrome 1+, opera9+, ff 3.5+ */
　　body:first-of-type #ocho { color: red }

　　/* saf3+, chrome1+ */
　　@media screen and (-webkit-min-device-pixel-ratio:0) {
　　　　#diez { color: red }
　　}

　　/* iPhone / mobile webkit */
　　@media screen and (max-device-width: 480px) {
　　　　#veintiseis { color: red }
　　}

　　/* Safari 2 - 3.1 */
　　html[xmlns*=""]:root #trece { color: red }

　　/* Safari 2 - 3.1, Opera 9.25 */
　　*|html[xmlns*=""] #catorce { color: red }

　　/* Everything but IE6-8 */
　　:root *> #quince { color: red }

　　/* IE7 */
　　*+html #dieciocho { color: red }

　　/* Firefox only. 1+ */
　　#veinticuatro, x:-moz-any-link { color: red }

　　/* Firefox 3.0+ */
　　#veinticinco, x:-moz-any-link, x:default { color: red }

　　/***** Attribute Hacks ******/

　　/* IE6 */
　　#once { _color: blue }

　　/* IE6, IE7 */
　　#doce { *color: blue; /* or #color: blue */ }

　　/* Everything but IE6 */
　　#diecisiete { color/**/: blue }

　　/* IE6, IE7, IE8 */
　　#diecinueve { color: blue\9; }

　　/* IE7, IE8 */
　　#veinte { color/*\**/: blue\9; }

　　/* IE6, IE7 -- acts as an !important */
　　#veintesiete { color: blue !ie; } /* string after ! can be anything */
```

#### 28.容器的水平和垂直居中[vertical-align用法](https://www.jianshu.com/p/dea069fecb62)

```css
.logo {
　　　　display: block;
　　　　text-align: center;
　　　　display: block;
　　　　text-align: center;
　　　　vertical-align: middle;
　　　　border: 4px solid #dddddd;
　　　　padding: 4px;
　　　　height: 74px;
　　　　width: 74px; }
```

#### 29.css阴影

```css 
外阴影：
.shadow {
　　　　-moz-box-shadow: 5px 5px 5px #ccc;
　　　　-webkit-box-shadow: 5px 5px 5px #ccc;
　　　　box-shadow: 5px 5px 5px #ccc;
　　}
内阴影：
.shadow {
　　　　-moz-box-shadow:inset 0 0 10px #000000;
　　　　-webkit-box-shadow:inset 0 0 10px #000000;
　　　　box-shadow:inset 0 0 10px #000000;
　　}
```

#### 30.取消IE文本框的滚动条

```css 
textarea { overflow: auto; } 
```

#### 31.图片预加载

请参考[3 Ways to Preload Images with CSS, JavaScript, or Ajax](http://perishablepress.com/press/2009/12/28/3-ways-preload-images-css-javascript-ajax/)。 

#### 32.css重置

请参考[Should You Reset Your CSS?](http://sixrevisions.com/css/should-you-reset-your-css/)。 







