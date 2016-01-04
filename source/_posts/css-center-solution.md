title: CSS水平垂直居中方法
date: 2015-09-15 09:16:02
categories: CSS3
tags:
---

<style>
	.css3-wrap {
		width: 200px;
		height: 200px;
		overflow: hidden;
		background-color: #CCC;
	}
	.css3-inner {
		height: 50px;
		width: 50px;
		margin: 0 auto;
		margin-top: calc((200px - 50px) / 2);
		background-color: black;
	}
	.css3-relative {
		position: relative;
		width: 100%;
		height: 200px;
		background-color: #CCC;
	}
	.css3-absolute {
		position: absolute;
		height: 50px;
		width: 50px;
		background-color: black;
		left: calc(50% - 50px / 2);
		top: calc(50% - 50px / 2);
	}
	.css3-wrap-float{
	    float: left;
	    width: 100%;
	    height: 200px;
	    background-color: #ccc;
	}
	.css3-ele{
	    float: left;
	    position: relative;
	    width: 400px;
	    left: 50%;
	    top: 50%;
	}
  	.css3-ele-inner{
	    position: relative;
	    left: -50%;
	    -webkit-transform : translate3d(0, -50%, 0);
	    transform : translate3d(0, -50%, 0);
	    background-color: #333;
	    color: #fff;
  	}

  	.css3-wrap-absolute {
		position: relative;
		width: 100%;
		height: 200px;
		background-color: #CCC;
	}
	.css3-ele-absolute {
		position: absolute;
		height: 50px;
		width: 50px;
		left: 0;
		top: 0;
		right: 0;
		bottom: 0;
		margin: auto;
		background-color: black;
	}
</style>

先来看一张图，下面这张图是在cssConf大会上@寒冬winter老师放出来的，在这里引用一下
![](../../../../images/css-center/css-center.png)
<!-- more-->
可以看到CSS的布局分为几块：
- 盒子内部布局
 - 文本的布局
 - 盒模型本身的布局
- 盒子之间的布局（visual formatting）
 - 脱离正常流的盒子布局
   - absolute布局以及上下文布局
   - float布局以及上下文布局
 - 正常流下盒子布局 
   - BFC布局上下文的布局
   - IFC布局上下文的布局
   - FFC布局上下文的布局
   - table布局上下文的布局
   - css grid布局上下文的布局

## 文本水平垂直居中
文本水平居中我们一般用text-align: center
垂直居中一般用line-height等于父容器宽度。
## 盒模型的水平垂直居中
我们用一个实例来分析
```html
<div class="wrap">
	<div class="inner"></div>
</div>
```
```css
	.wrap {
		width: 200px;
		height: 200px;
		overflow: hidden;
		background-color: #CCC;
	}
	.inner {
		height: 50px;
		width: 50px;
		margin: 0 auto;
		margin-top: calc((200px - 50px) / 2);
		background-color: black;
	}
```
<div class="css3-wrap"><div class="css-3inner"></div></div>

这里水平居中用**margin： 0 auto**来做，垂直方向上用**calc**计算出inner需要距离wrap顶部的距离。还有一种方式是利用padding填充的方式，方法其实跟上面一样的道理，如下:
```css
	.wrap {
		width: 200px;
		height: 200px;
		background-color: #CCC;
	}
	.inner {
		height: 50px;
		width: 50px;
		margin: 0 auto;
		padding: calc((200px - 50px) / 2);
		background-color: black;
		backgrund-clip: content-box;
	}
```
## absolute元素垂直水平居中
第一种方法：
```html
	<div class="relative">
		<div class="absolute"></div>
	</div>
```
```css
	.relative {
		position: relative;
		width: 100%;
		height: 200px;
		background-color: #CCC;
	}
	.absolute {
		position: absolute;
		height: 50px;
		width: 50px;
		background-color: black;
		left: calc(50% - 50px / 2);
		top: calc(50% - 50px / 2);
	}
```
<div class="css3-relative"><div class="css3-absolute"></div></div>

这里利用计算left值，居中刚好就是父元素的宽度（高度）一半减去自己宽度（高度）的一半。

第二种方法：
```html
	<div class="wrap">
		<div class="ele"></div>
	</div>
```
```css
	.wrap {
		position: relative;
		width: 100%;
		height: 200px;
		background-color: #CCC;
	}
	.ele {
		position: absolute;
		height: 50px;
		width: 50px;
		left: 0;
		top: 0;
		right: 0;
		bottom: 0;
		margin: auto;
		background-color: black;
	}
```
<div class="css3-wrap-absolute"><div class="css3-ele-absolute"></div></div>

对于这种情况，比较特殊，W3C里有这么一句话：
> The constraint that determines the used values for these elements is: 
left+margin-left+border-left-width+padding-left+width+padding-right+border-right-width+margin-right+right=width of containing block

意思就是absolute性质盒子的包含快宽度=盒模型宽度+left+right，高度同理。这里我们设置ele元素的left、right为0，width为定值，所以margin-left+margin-right+width=包含快的宽度，W3C规定当left、right、width都不为auto时候，如果margin-left、margin-right为auto,那么他们的值相等。也就是说均分了包含快宽度-width剩下的宽度，垂直方向居中同样用的这个原理。


## float上下文的水平垂直居中布局
```html
    <div class="wrap-float">  
        <div class="ele">
            <div class="ele-inner">
                Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
                Enim veniam ipsam aspernatur nostrum et deserunt quos, id quae quam voluptates cupiditate ducimus fuga,
                alias similique dolorum eaque ex corrupti minima?
                </div>
            </div>
    </div> 
```
```css
.wrap{
    float: left;
    width: 100%;
    height: 400px;
    background-color: #ccc;
}
.ele{
    float: left;
    position: relative;
    width: 400px;
    left: 50%;
    top: 50%;
}
.ele-inner{
    position: relative;
    left: -50%;
    -webkit-transform : translate3d(0, -50%, 0);
    transform : translate3d(0, -50%, 0);
    background-color: #333;
    color: #fff;
}
```
效果如下：
<div class="css3-wrap-float">  <div class="css3-ele"><div class="css3-ele-inner">Lorem ipsum dolor sit amet, consectetur adipisicing elit. Enim veniam ipsam aspernatur nostrum et deserunt quos, id quae quam voluptates cupiditate ducimus fuga, alias similique dolorum eaque ex corrupti minima?</div></div></div>  

这里真正最后居中的元素是ele-inner,原理就是用ele容器用left偏移50%父容器宽度，然后在ele-inner中向左偏移-50%父容器的宽度（就是ele元素的宽度一半），垂直方向上因为不知道ele的宽度，这里我们利用CSS3的transform在Y轴上偏移父容器-50%宽度，这里不能用top:-50%的原因就是因为ele没有固定宽度，学到了吧，新技能get。

## BFC上下文布局
首先我们要搞清楚什么事BFC？BFC全称Box Formatting Context,也就是块级排版上下文，那些元素是属于块级元素呢？参考[W3C标准](http://www.w3.org/TR/CSS2/visuren.html#block-formatting)我们知道，display属性为block、table、list-item的元素属于块级元素。我们来看一张标准的盒模型图：
![](../../../../images/css-center/box-level.png)
BFC的布局规则有如下几点：

* BFC内部块级盒会再垂直方向，一个挨一个放置。
* 内部的块级盒垂直方向距离由margin决定，并且相邻的块级盒的margin会重叠。
* 每个块级盒的margin box左边会与BFC包含块border box的左边接触，即使浮动也是一样。
* BFC区域不会与float box重叠。
* BFC就是页面上一个隔离的容器，容器里面的元素不会影响外面，外面也不会影响里面。
* 计算BFC容器高度时候，浮动元素也要参与计算。

* 浮动元素，绝对定位元素，inline-block、table-cell、table-capation以及overflow不为visible的元素会产生一个新的BFC。
* float元素、absolute元素脱离了normal flow不会参与到BFC的布局中。
* BFC中的块级盒他们相邻的margin是重叠的。
前两条比较好理解，最后一条需要注意，就是BFC中两个相邻块级盒中间只有一个margin距离，因为重叠了。如果不想让他们重叠，可以让他们的父元素称为一个新的BFC
