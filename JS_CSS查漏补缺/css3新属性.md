### （1）边框：

border-radius：圆角边框，border-radius:25px;
box-shadow：边框阴影，box-shadow: 10px 10px 5px #888888;
border-image：边框图片，border-image:url(border.png) 30 30 round;

### （2）背景：

background-size：规定背景图片的尺寸，background-size:63px 100px;
background-origin：规定背景图片的定位区域，背景图片可以放置于 content-box、padding-box 或 border-box 区域。background-origin:content-box;

CSS3 允许您为元素使用多个背景图像。background-image:url(bg_flower.gif),url(bg_flower_2.gif);

### （3）文本效果：

text-shadow：向文本应用阴影，可以规定水平阴影、垂直阴影、模糊距离，以及阴影的颜色。text-shadow: 5px 5px 5px #FF0000;

word-wrap：允许文本进行换行。word-wrap:break-word;

### （4）字体：CSS3 @font-face 规则可以自定义字体。

### （5）2D 转换（transform）

translate()：元素从其当前位置移动，根据给定的 left（x 坐标） 和 top（y 坐标） 位置参数。 transform: translate(50px,100px);
rotate()：元素顺时针旋转给定的角度。允许负值，元素将逆时针旋转。transform: rotate(30deg);
scale()：元素的尺寸会增加或减少，根据给定的宽度（X 轴）和高度（Y 轴）参数。transform: scale(2,4);
skew()：元素翻转给定的角度，根据给定的水平线（X 轴）和垂直线（Y 轴）参数。transform: skew(30deg,20deg);
matrix()： 把所有 2D 转换方法组合在一起，需要六个参数，包含数学函数，允许您：旋转、缩放、移动以及倾斜元素。transform:matrix(0.866,0.5,-0.5,0.866,0,0);

### （6）3D 转换

rotateX()：元素围绕其 X 轴以给定的度数进行旋转。transform: rotateX(120deg);
rotateY()：元素围绕其 Y 轴以给定的度数进行旋转。transform: rotateY(130deg);

### （7）transition：过渡效果，使页面变化更平滑

transition-property：执行动画对应的属性，例如 color，background 等，可以使用 all 来指定所有的属性。
transition-duration：过渡动画的一个持续时间。
transition-timing-function：在延续时间段，动画变化的速率，常见的有：ease | linear | ease-in | ease-out | ease-in-out | cubic-bezier 。
transition-delay：延迟多久后开始动画。
简写为：transition: [<transition-property> || <transition-duration> || <transition-timing-function> || <transition-delay>];

### （8）animation：动画

使用CSS3 @keyframes 规则。

animation-name: 定义动画名称
animation-duration: 指定元素播放动画所持续的时间长
animation-timing-function:ease | linear | ease-in | ease-out | ease-in-out | cubic-bezier(<number>, <number>, <number>, <number>)： 指元素根据时间的推进来改变属性值的变换速率，说得简单点就是动画的播放方式。
animation-delay: 指定元素动画开始时间
animation-iteration-count:infinite | <number>：指定元素播放动画的循环次
animation-direction: normal | alternate： 指定元素动画播放的方向，其只有两个值，默认值为normal，如果设置为normal时，动画的每次循环都是向前播放；另一个值是alternate，他的作用是，动画播放在第偶数次向前播放，第奇数次向反方向播放。
animation-play-state:running | paused：控制元素动画的播放状态。
简写为：animation:[<animation-name> || <animation-duration> || <animation-timing-function> || <animation-delay> || <animation-iteration-count> || <animation-direction>]

这里只列出了一部分，详情可以去看w3school的CSS3 教程。