[TOC]

# canvas

### 1 准备

```HTML
<canvas id="test-canvas" width="300" height="200"></canvas>
```

- 建议永远不要使用css来设定canvas的宽高
- 由于浏览器对HTML5标准支持不一致，所以，通常在`<canvas>`内部添加一些说明性HTML代码，如果浏览器支持Canvas，它将忽略`<canvas>`内部的HTML，如果浏览器不支持Canvas，它将显示`<canvas>`内部的HTML： 

```HTML 
<canvas id="test-stock" width="300" height="200">
    <p>Current Price: 25.51</p>
</canvas>
```

- 在使用Canvas前，用`canvas.getContext`来测试浏览器是否支持Canvas 

- ```JavaScript
  var canvas = document.getElementById('test-canvas');
  if (canvas.getContext) {
      console.log('你的浏览器支持Canvas!');
  } else {
      console.log('你的浏览器不支持Canvas!');
  }
  ```

- `getContext('2d')`方法让我们拿到一个`CanvasRenderingContext2D`对象，所有的绘图操作都需要通过这个对象完成。 

  ```JavaScript
  var ctx = canvas.getContext('2d');
  // 绘制3D：
  gl = canvas.getContext("webgl");
  ```

### 2 绘制

Canvas坐标系：

![](D:\文件\前端\笔记\JS_CSS查漏补缺\imgs\canvas坐标系.png)

`CanvasRenderingContext2D`对象有若干方法来绘制图形： 

#### **矩形&路径**

```JavaScript
'use strict';
var
    canvas = document.getElementById('test-shape-canvas'),
    ctx = canvas.getContext('2d');
ctx.clearRect(0, 0, 200, 200); // 擦除(0,0)位置大小为200x200的矩形，擦除的意思是把该区域变为透明
ctx.fillStyle = '#dddddd'; // 设置颜色
ctx.fillRect(10, 10, 130, 130); // 把(10,10)位置大小为130x130的矩形涂色
// 利用Path绘制复杂路径:
var path=new Path2D(); //表示开始绘制路径
path.arc(75, 75, 50, 0, Math.PI*2, true);// arc(圆心，半径，起始角度，是否逆时针)
path.moveTo(110,75);// 路径游标变换位置
path.arc(75, 75, 35, 0, Math.PI, false);
path.moveTo(65, 65);
path.arc(60, 65, 5, 0, Math.PI*2, true);
path.moveTo(95, 65);
path.arc(90, 65, 5, 0, Math.PI*2, true);
ctx.strokeStyle = '#0000ff';	// 设置路径颜色
ctx.stroke(path);	// 绘制路径
```

- 补充：lineTo(x,y)：从上一点开始绘制一条直线，到(x,y)为止

  rect(x,y,width,height)：从(x,y)绘制一个矩形，宽度和高度。绘制的是矩形路径，而不是独立形状

  arcTo(x1,y1,x2,y2,radius)：从上一点开始绘制一条弧线，到(x2,y2)为止，穿过(x1,y1)，半径

  bezierCurveTo(c1x,c1y,c2x,c2y,x,y)：从上一点绘制到(x,y)，以两个c点为控制点

  quadraticCurveTo(cx,cy,x,y)：从上一点开始绘制一条二次曲线，到(x,y)为止，以c点为控制点

#### **绘制文本**

```JavaScript
ctx.clearRect(0, 0, canvas.width, canvas.height);// 清空绘图区域
ctx.shadowOffsetX = 2; // x轴阴影
ctx.shadowOffsetY = 2;// y轴阴影
ctx.shadowBlur = 2;// 模糊像素数
ctx.shadowColor = '#666666';// 阴影颜色
ctx.font = '24px Arial';
ctx.fillStyle = '#333333';
ctx.fillText('带阴影的文字', 20, 40);
```

:up:fillText/strokeText('文本字符串',x,y,最大像素宽度)，stroke：描边

​	font：表示文字样式、大小、字体，类css样式

​	textAlign：文本对齐方式。可能值：start、end、left、right、center

​	textBaseline：文本的基线。可能值：top（y轴为文字顶端）、hanging、middle、alphabetical、ideographic、bottom（y轴为文字顶端）

:warning:Canvas除了能绘制基本的形状和文本，还可以实现动画、缩放、各种滤镜和像素转换等高级操作。如果要实现非常复杂的操作，考虑以下优化方案：

- 通过创建一个不可见的Canvas来绘图，然后将最终绘制结果复制到页面的可见Canvas中；
- 尽量使用整数坐标而不是浮点数；
- 可以**创建多个重叠的Canvas绘制不同的层**，而不是在一个Canvas中绘制非常复杂的图；
- 背景图片如果不变可以直接用`<img>`标签并放到最底层。

#### **变换**

rotate(angle)：围绕原点旋转图像

scale(scaleX,scaleY)：缩放图像，x方向乘以scaleX...，默认都是1.0

translate(x,y)：将坐标原点移动到(x,y)

transform(m1_1,m1_2,m2_1,m2_2,dx,dy)：直接修改变换矩阵，方式是乘以下列矩阵

| m1_1 | m1-2 |  dx  |
| :--: | :--: | :--: |
| m2_1 | m2_2 |  dy  |
|  0   |  0   |  1   |

setTransform(m1_1,m1_2,m2_1,m2_2,dx,dy)：将变换矩阵重置为默认状态，在调用上述方法

#### **绘制图像**

```JavaScript
var img = document.images[0]
content.drawImage(得到的图像，源图x坐标，源图y坐标，源图宽，源图高，目标图x，目标图y，目标图宽，目标图高) 
```

#### **渐变**

```JavaScript
var gradient = content.createLinearGradient(起点x，起点y，终点x，终点y)//返回一个指定大小的渐变的实例
addColorStop(色标位置，css颜色值)//色标位置是0-1的数字
// 使用渐变实例
context.fillStyle = gradient // 得到渐变模板
context.fillRect(20,20,70,70) // 绘制渐变矩形
```

径向渐变/放射渐变：

createRadialGradient()：加收6个参数，起点园圆心(x,y)及半径，终点圆的圆心及半径。想象成一个长圆筒，圆为两个开口的位置。

#### **模式**

重复的图像，描边or填充

```javascript
var image = document.images[0]
var pattern = content.createPattern(image,'repeat/repeat-x/repeat-y/no-repeat')
// image的参数也可以是一个<video>元素，或另一个<canvas>元素
content.fillStyle = pattern
content.fillRect(0,0,100,100)
```

#### **使用图像数据**

将图像设置为黑白，或滤镜等操作

```javascript
var image = document.images[0]
context.drawImage(image,0,0)// 绘制原始图像
var imaageData = context.getImageData(画面区域x，画面区域y，像素宽，像素高) // 取得图像数据
var data = imageData.data  
var red = data[0]  // 源图的red值，[0,255]
var green = data[1]
var blue = data[2]
vae alpha = data[3] // 透明度 [0,255]
// 修改图像数据
imageData = data // 修改后的data
// 回写图像数据，并显示结果
context.putImageData(imageData,0,0)
```

#### **合成**

```JavaScript
content.globalAlpha = 0~1
// 介于0-1的全局透明度，如果要所有后续操作都基于相同透明度，就先把globalAlpha设置为适当的值，然后绘制，最后在设回默认值0

context.globalCompositionOperation = '' // 表示后绘制的图形怎样与先绘制的图形结合
source-over：后绘制的在上方（默认）
source-in：重叠部分可见，其他部位透明
source-out：后图与先图不重叠部分可见，先图完全透明
source-atop：重叠部分可见，先图不受影响
destination-over：后图在下方
destination-in：后图在下方，重叠可见
destination-out：后图擦除与先图重叠部分
destination-atop：先图的不重叠部分透明
lighter：重叠部分值相加，变量
copy：后图完全代替先图的重叠部分
xor：重叠部分执行异或操作
```



### 1.3 与贝塞尔曲线的结合

其中Canvas有个**bezierCurveTo()**方法，代码如下： 

```JavaScript
var c=document.getElementById("myCanvas");
var ctx=c.getContext("2d");
ctx.beginPath();
ctx.moveTo(20,20);
ctx.bezierCurveTo(20,100,200,100,200,20);
ctx.stroke();

开始点：moveTo(20,20)
控制点 1：bezierCurveTo(20,100,200,100,200,20)
控制点 2：bezierCurveTo(20,100,200,100,200,20)
结束点：  bezierCurveTo(20,100,200,100,200,20)
```















