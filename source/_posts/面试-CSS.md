---
title: 面试-css
category: 面试
tag: [面试, css]
---

box模型, link标签和import标签的区别, Flex布局, 浮动清除, css3新特性

<!--more-->

## box模型

标准box模型

![8-18-1](/img/8-18-1.png)

IE盒子模型：区别是width 把border也包括了

模型选择: CSS3中引入了box-sizing属性. 

box-sizing:**content-box** -> 表示标准的盒子模型

box-sizing:**border-box** -> IE盒子模型

##  link标签和import标签的区别

>  ```html
>  <link rel="stylesheet" type="text/css" href="">
>  ```

link属于html标签，页面被加载时，link会同时被加载.

[@import](https://segmentfault.com/a/1190000000369549)是在css提供的, @import引用的css会等到页面加载结束后加载。

link是html标签都能用，而@import只有IE5以上才能识别。

link方式样式的权重高于@import的。

**import规则一定要先于除了@charset的其他任何CSS规则** 否则不会触发import请求.

因为import的确会带来一些问题，所以网络上会有各种「抵制@import」的文章，[高性能网站设计：不要使用@import](http://www.qianduan.net/high-performance-web-site-do-not-use-import.html)

@media 语句:

在下面的 CSS中，我们将给 `<body>` 元素一个粉红色的背景色。但是，我们随后使用@media创建样式表的一个部分，该部分仅适用于视口大于30em的浏览器。如果浏览器的宽度大于30em，则背景色将为蓝色。

```css
body {
  background-color: pink;
}

@media (min-width: 30em) {
  body {
    background-color: blue;
  }
}
```



## Flex布局

Flex是Flexible Box的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。
布局的传统解决方案，基于盒状模型，依赖display属性 + position属性 + float属性。它对于那些特殊布局非常不方便，比如，**垂直居中**就不容易实现。

> ? 我一般使用boostrap row col-md-6这样

### 垂直居中

4)利用flex

将父元素设置为display:flex，并且设置align-items:center;justify-content:center;

```css
css:
.container{
    width: 300px;
    height: 200px;
    border: 3px solid #546461;
    display: -webkit-flex;
    display: flex;
    -webkit-align-items: center;
    align-items: center;
    -webkit-justify-content: center;
    justify-content: center;
}
.inner{
    border: 3px solid #458761;
    padding: 20px;
}
```

## 块级元素和行内元素

块级元素 block：**p** **div** 独占一行，并且有自动填满父元素，可以设置margin和pading以及高度和宽度

![block element](/img/block_element.png)

行内元素 inline：**span** 不会独占一行，width和height会失效，并且在垂直方向的padding和margin会失效。![inline element](/img/inline_element.png)

转换:

 a. display:none;不显示该元素，也不会保留该元素原先占有的文档流位置。
 b. display:block;转换为块级元素。
 c. display:inline;转换为行内元素。
 d. display:inline-block;转换为行内块级元素。

## visibility=hidden, opacity=0，display:none

opacity=0，该元素隐藏，**不会改变页面布局**，绑定一些事件，如click事件，那么点击该区域，也能**触发点击事件**的

visibility=hidden，该元素隐藏起来了，**不会改变页面布局**，但是**不会触发**该元素已经绑定的事件

display=none，把元素隐藏起来，**改变页面布局**，可以理解成在页面中把该元素**删除掉一样**。

## position属性 比较

固定定位**fixed**：

元素的位置**相对于浏览器窗口是固定位置**，即使窗口是滚动的它也不会移动。Fixed定位使元素的位置与文档流无关，因此不占据空间。 Fixed定位的元素和其他元素重叠。(如navbar

相对定位**relative**：

如果对一个元素进行相对定位，它将出现在它所在的位置上。然后，可以通过设置垂直或水平位置，让这个元素“相对于”它的起点进行移动。 在使用相对定位时，无论是否进行移动，元素仍然占据原来的空间。因此，移动元素会导致它覆盖其它框。

绝对定位**absolute**：

绝对定位的元素的位置**相对于最近的已定位父元素**，如果元素没有已定位的父元素，那么它的位置相对于<html>。 absolute 定位使元素的位置与文档流无关，因此不占据空间。 absolute 定位的元素和其他元素重叠。

粘性定位**sticky**：

元素先按照普通文档流定位，然后相对于该元素在流中的flow root（BFC）和 containing block（最近的块级祖先元素）定位。而后，元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。

默认定位Static：

默认值。没有定位，元素出现在**正常的流中**（忽略top, bottom, left, right 或者 z-index 声明）。

inherit:

规定应该从父元素继承position 属性的值。

## 浮动清除

? 没听说过. 参考https://www.cnblogs.com/ForEvErNoME/p/3383539.html

方法一：使用带clear属性的空元素

在浮动元素后使用一个空元素如`<div class="clear"></div>`，并在CSS中赋予.clear{clear:both;}属性即可清理浮动。亦可使用`<br class="clear" />`或`<hr class="clear" />`来进行清理。

方法二：使用CSS的overflow属性

给浮动元素的容器添加overflow:hidden;或overflow:auto;可以清除浮动，另外在 IE6 中还需要触发 hasLayout ，例如为父元素设置容器宽高或设置 zoom:1。

在添加overflow属性后，浮动元素又回到了容器层，把容器高度撑起，达到了清理浮动的效果。

## css3新特性

1. CSS3边框： border-radius边角，box-shadow

2. CSS3背景：background-size：  CSS3 之前，背景图片的尺寸是由图片的实际尺寸决定的。在 CSS3 中，可以规定背景图片的尺寸，这就允许我们在不同的环境中重复使用背景图片

3. CSS3文字效果：text-shadow.  word-wrap :单词太长的话就可能无法超出某个区域，允许对长单词进行拆分，并换行到下一行：p{word-wrap:break-word;}

4. CSS3 2D转换, 3D转换, 动画