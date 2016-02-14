title: CSS3之Flex布局基础
date: 2016-02-14 11:47:17
categories: CSS
tags: [CSS, Flexiable Box]
---

### 什么是Flex布局
2015年5月14日，W3C的CSS工作组发布了CSS弹性盒式布局模块（[CSS Flexible Box Layout Module Level 1](https://www.w3.org/TR/2015/WD-css-flexbox-1-20150514/)）的标准草案最终征求意见稿。该文档描述了一个面向用户接口设计的CSS盒式模型（box model）。在弹性布局（flex layout）模型中，一个弹性容器（flex container）的子容器可以在任何方向布局，并可以灵活改变其尺寸，如增加大小填充空余的空间，或自动缩小以避免溢出父容器的区域。也很容易实现子元素的横向对齐和纵向对齐。Flex布局被称作是下一代的布局解决方案。

<!-- more -->

### 基本概念
采用Flex布局的元素称为Flex容器，里面所有元素默认都是`flex item`，看一张W3C的图：

![](img/flex-direction-terms.svg)

Flex容器有两根轴，分别是`主轴(main axis)`和`交叉轴(cross axis)`，主轴开始位置是`main start`，结束位置是`main end`，项目在主轴占据的空间距离叫`main size`，交叉轴开始位置是`cross start`，结束位置是`cross end`，项目在交叉轴上占的空间距离叫`cross size`。
### 容器的属性
下面这些属性是用在容器上的：
- flex-direction
- flex-wrap
- flex-flow
- justify-content
- align-items
- align-content

下面我们来一一介绍这些属性的作用
#### flex-direction
`flex-direction`是设置主轴的方向，默认是水平轴从左往右排列。它还可以设置下面这些属性
```css
flex-direction: row | row-reverse | column | column-reverse
```
- row ：默认值，主轴水平从左往右。
- row-reverse ：主轴水平从右往左。
- column ：主轴垂直从上到下。
- column-reverse ：主轴垂直从下到上。

#### flex-wrap
`flex-wrap`是用来设置当容器内部元素把容器宽度占满后如何换行。
```css
flex-wrap: nowrap | wrap | wrap-reverse
```
- nowrap ：默认值，不换行。
- wrap ：换行，前一行在上面。
- wrap-reverse ：换行，前一行在下面。

#### flex-flow
`flex-flow`是`flex-direction`和`flex-wrap`的简写，默认为`flex-flow: row nowrap`。
#### justify-content
`justify-content`是用来控制主轴方向的对齐方式。
```css
justify-content: flex-start | flex-end | center | space-between | space-around;
```
- flex-start ：默认值，左对齐。
- flex-end ：右对齐。
- center ：居中。
- space-between ：两端对齐，`item`之间的间距都相等。
- space-around ：每个`item`的两侧间隔相等。

#### align-items
`align-items`用来定义交叉轴的对齐方式：
```css
align-items: flex-start | flex-end | center | baseline | stretch;
```
- flex-start ：交叉轴起点对齐。
- flex-end ：交叉轴终点对齐。
- center ：交叉轴中点对齐。
- baseline ：项目第一行文字基线对齐。
- stretch ：默认值，`item`如果不设置高度或`auto`，默认占满容器。

#### align-content
`align-content`定义了多根轴线的对齐方式。
```css
align-content: flex-start | flex-end | center | space-between | space-around | stretch;
```
- flex-start：与交叉轴的起点对齐。
- flex-end：与交叉轴的终点对齐。
- center：与交叉轴的中点对齐。
- space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
- space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
- stretch（默认值）：轴线占满整个交叉轴。

### 项目的属性
项目的属性包括：
- order
- flex-grow
- flex-shrink
- flex-basis
- flex
- align-self

#### order
`order`用来定义项目的排列顺序，数字越小越靠前，默认值为0.
```css
order: <number>;
```
#### flex-grow
`flex-grow`定义`item`的放大比例，默认值为0.
```css
flex-grow: <number>;
```
`item`的`flex-grow`都设置为1，则会均分的占据父容器宽度，如果其中一个为2，则这个2的item宽度是其他item的两倍。
#### flex-shrink
`flex-shrink`用来定义`item`的缩小比例，默认为1.
```css
flex-shrink: <number>;
```
如果当item的`flex-shrink`都是1，当空间不足时，将会等比例缩小。
#### flex-basis
`flex-basis`属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
#### flex
`flex`其实是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。
#### align-self
`align-self`允许单个项目跟其他项目不同的对齐方式，用来覆盖父级的`align-items`。可以获取的属性值跟`align-items`一致。

### 参考文章

[Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html) *By 阮一峰*
[CSS Flexible Box Layout Module Level 1](https://www.w3.org/TR/2015/WD-css-flexbox-1-20150514/) *By W3C*
[一个完整的Flexbox指南](http://www.w3cplus.com/css3/a-guide-to-flexbox.html) *by 大漠*