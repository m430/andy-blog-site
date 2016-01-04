title: CSS3画三角形
date: 2015-09-02 15:48:29
categories: CSS3
tags:
---

<style>
	.box {
		margin: 20px auto;
		width: 0;
		height: 0;
		border: 50px solid transparent;
		border-top-color: red;
		border-right-color: yellow;
		border-bottom-color: royalblue;
		border-left-color: green;
	}
	.top-triangle,.right-triangle,.bottom-triangle,.left-triangle {
		width: 0;
		height: 0;
		border: 50px solid transparent;
		display: inline-block;
	}
	.top-triangle {
		border-top-color: red;
	}
	.right-triangle {
		border-right-color: yellow;
	}
	.bottom-triangle {
		border-bottom-color: royalblue;
	}
	.left-triangle {
		border-left-color: green;
	}
</style>

前端开发经常会碰到写小三角的情况，以前我们都是用图片来完成，不过同样可以用CSS来写小三角。
其实用CSS来写也很简单，下面我们通过一个实例可以来看一下。

首先，HTML如下：
```html
	<div class="box"></div>
```
<!-- more-->
CSS代码如下：
```css
	.box {
		width: 0;
		height: 0;
		border: 50px solid transparent;
		border-top-color: red;
		border-right-color: yellow;
		border-bottom-color: royalblue;
		border-left-color: green;
	}
```
然后你会看到如下的样子
<div class="box">
</div>

其实原理很简单，就是把div的高度和宽度设为0，通过设置边框的宽度以及颜色来生成小三角的样子。这样如果想做不同方向的小三角就可以这样：
```css
	.top-triangle,.right-triangle,.bottom-triangle,.left-triangle {
		width: 0;
		height: 0;
		border: 50px solid transparent;
	}
	.top-triangle {
		border-top-color: red;
	}
	.right-triangle {
		border-right-color: yellow;
	}
	.bottom-triangle {
		border-bottom-color: royalblue;
	}
	.left-triangle {
		border-left-color: green;
	}

```
<div class="top-triangle"></div><div class="right-triangle"></div><div class="bottom-triangle"></div><div class="left-triangle"></div>

是不是很简单，这样就可以制作各种三角了。