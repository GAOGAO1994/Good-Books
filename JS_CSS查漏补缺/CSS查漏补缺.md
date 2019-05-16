[TOC]

# CSS查漏补缺

## vertical-align 

属性设置元素的垂直对齐方式。

**说明**

该属性定义行内元素的基线相对于该元素所在行的基线的垂直对齐。允许指定负长度值和百分比值。这会使元素降低而不是升高。在表单元格中，这个属性会设置单元格框中的单元格内容的对齐方式。

> - 值	描述
> - baseline默认。元素放置在父元素的基线上。
> - sub垂直对齐文本的下标。
> - super垂直对齐文本的上标
> - top把元素的顶端与行中最高元素的顶端对齐
> - text-top把元素的顶端与父元素字体的顶端对齐
> - middle把此元素放置在父元素的中部。
> - bottom把元素的顶端与行中最低的元素的顶端对齐。
> - text-bottom把元素的底端与父元素字体的底端对齐。
> - length 
> - %使用 "line-height" 属性的百分比值来排列此元素。允许使用负值。
> - inherit规定应该从父元素继承 vertical-align 属性的值。

## CSS direction 属性

## 定义和用法

direction 属性规定文本的方向 / 书写方向。

该属性指定了块的基本书写方向，以及针对 Unicode 双向算法的嵌入和覆盖方向。不支持双向文本的用户代理可以忽略这个属性。

**可能的值**

| 值      | 描述                                      |
| :------ | :---------------------------------------- |
| ltr     | 默认。文本方向从左到右。                  |
| rtl     | 文本方向从右到左。                        |
| inherit | 规定应该从父元素继承 direction 属性的值。 |

## CSS white-space 属性

**实例**

规定段落中的文本不进行换行：

```
p{
  white-space: nowrap
  }
```

**可能的值**

| 值       | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| normal   | 默认。空白会被浏览器忽略。                                   |
| pre      | 空白会被浏览器保留。其行为方式类似 HTML 中的 <pre> 标签。    |
| nowrap   | 文本不会换行，文本会在在同一行上继续，直到遇到 <br> 标签为止。 |
| pre-wrap | 保留空白符序列，但是正常地进行换行。                         |
| pre-line | 合并空白符序列，但是保留换行符。                             |
| inherit  | 规定应该从父元素继承 white-space 属性的值。                  |

## CSS有哪些继承属性

- ### 关于文字排版的属性如：

  - [font](https://developer.mozilla.org/en-US/docs/Web/CSS/font)
  - [word-break](https://developer.mozilla.org/en-US/docs/Web/CSS/word-break)
  - [letter-spacing](https://developer.mozilla.org/en-US/docs/Web/CSS/letter-spacing)
  - [text-align](https://developer.mozilla.org/en-US/docs/Web/CSS/text-align)
  - [text-rendering](https://developer.mozilla.org/en-US/docs/Web/CSS/text-rendering)
  - [word-spacing](https://developer.mozilla.org/en-US/docs/Web/CSS/word-spacing)
  - [white-space](https://developer.mozilla.org/en-US/docs/Web/CSS/white-space)
  - [text-indent](https://developer.mozilla.org/en-US/docs/Web/CSS/text-indent)
  - [text-transform](https://developer.mozilla.org/en-US/docs/Web/CSS/text-transform)
  - [text-shadow](https://developer.mozilla.org/en-US/docs/Web/CSS/text-shadow)

- [line-height](https://developer.mozilla.org/en-US/docs/Web/CSS/line-height)

- [color](https://developer.mozilla.org/en-US/docs/Web/CSS/color)

- [visibility](https://developer.mozilla.org/en-US/docs/Web/CSS/visibility)

- [cursor](

## CSS3 opacity 属性

设置 div 元素的不透明级别：

**语法**

```
opacity: value|inherit;
```

| 值      | 描述                                                    | 测试                                                       |
| :------ | :------------------------------------------------------ | :--------------------------------------------------------- |
| *value* | 规定不透明度。从 0.0 （完全透明）到 1.0（完全不透明）。 | [测试](http://www.w3school.com.cn/tiy/c.asp?f=css_opacity) |
| inherit | 应该从父元素继承 opacity 属性的值。                     |                                                            |

```HTML
<!DOCTYPE html>
<html>
<head>
<script>
function ChangeOpacity(x)
{
// 返回被选选项的文本
var opacity=x.options[x.selectedIndex].text;
var el=document.getElementById("p1");
if (el.style.opacity!==undefined)
  {el.style.opacity=opacity;}
else
  {alert("Your browser doesn't support this example!");}
}
</script>
</head>
<body>

<p id="p1">请从下面的例子中选择一个值，以改变此元素的不透明度。</p>
<select onchange="ChangeOpacity(this);" size="5">
  <option />0
  <option />0.2
  <option />0.5
  <option />0.8
  <option selected="selected" />1
</select>

</body>
</html>
```

## [层叠上下文stacking context布局规则](https://blog.csdn.net/yangxiaoyanger/article/details/79662925)

![img](https://camo.githubusercontent.com/79a1aa1dc3ac671b2fe08ce35ab8b464a599c3b5/687474703a2f2f696d616765732e636e626c6f67732e636f6d2f636e626c6f67735f636f6d2f636f636f31732f3838313631342f6f5f737461636b696e676c6576656c2e706e67)

> 1. 形成堆叠上下文环境的元素的背景与边框
>
> 2. #### 拥有负 `z-index` 的子堆叠上下文元素 （负的越高越堆叠层级越低）
>
> 3. #### 正常流式布局，非 `inline-block`，无 `position` 定位（static除外）的子元素
>
> 4. #### 无 `position` 定位（static除外）的 float 浮动元素
>
> 5. #### 正常流式布局， `inline-block`元素，无 `position` 定位（static除外）的子元素（包括 display:table 和 display:inline ）
>
> 6. #### 拥有 `z-index:0` 的子堆叠上下文元素
>
> 7. 拥有正 `z-index:` 的子堆叠上下文元素（正的越低越堆叠层级越低）

添加的 `opacity:0.9` 这个让两个 div 都生成了 `stacking context（堆叠上下文）` 的概念。此时，要对两者进行层叠排列，就需要 z-index ，z-index 越高的层叠层级越高。

**堆叠上下文**是HTML元素的三维概念，这些HTML元素在一条假想的相对于面向（电脑屏幕的）视窗或者网页的用户的 z 轴上延伸，HTML 元素依据其自身属性按照优先级顺序占用层叠上下文的空间。

> 那么，如何触发一个元素形成 `堆叠上下文` ？方法如下，摘自 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Understanding_z_index/The_stacking_context)：
>
> - 根元素 (HTML),
> - z-index 值不为 "auto"的 绝对/相对定位，
> - 一个 z-index 值不为 "auto"的 flex 项目 (flex item)，即：父元素 display: flex|inline-flex，
> - opacity 属性值小于 1 的元素（参考 the specification for opacity），
> - transform 属性值不为 "none"的元素，
> - mix-blend-mode 属性值不为 "normal"的元素，
> - filter值不为“none”的元素，
> - perspective值不为“none”的元素，
> - isolation 属性被设置为 "isolate"的元素，
> - position: fixed
> - 在 will-change 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值
> - -webkit-overflow-scrolling 属性被设置 "touch"的元素

[Measuring Element Dimension and Location with CSSOM in Windows Internet Explorer 9](http://msdn.microsoft.com/en-us/library/ie/hh781509(v=vs.85).aspx)

![元素尺寸](D:/%E6%96%87%E4%BB%B6/%E5%89%8D%E7%AB%AF/%E7%AC%94%E8%AE%B0/js%E9%9D%A2%E8%AF%95%E9%A2%98/img/element-size.png)

伪类：before，after