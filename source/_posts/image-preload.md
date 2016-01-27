title: JS实现图片预加载和懒加载
date: 2016-01-26 10:47:56
categories: Javascript
tags: [Javascript, preload, lazyload]
---

### 预加载
使用图片预加载可以获得更好的用户体验，比如有这样一种需求，在常见的轮播图中，如果有10000张图片，我们不可能把所有图片一次性加入，那么用户要等到所有图片加载完成后才能翻看，体验度很差，这个时候“预加载”就派上用场了，怎么实现图片的预加载呢？我们看看下面这段代码：
```javascript
var img = new Image();
img.src = 'img/1.jpg';
img.onload = function() {
// 执行图片的onload方法浏览器会把设置src的图片加载到缓存当中
}
```
<!-- more -->
是的，就是利用**Image**对象来预加载图片到浏览器缓存中，当用户再次需要访问这张图片的时候就会直接从缓存中访问，不需要再次下载一遍。我们来做一个demo，点击图片切换观看图片，非常简单，但是我们用预加载的方式来实现一下：
```javascript
window.onload = function() {
	// 创建图片元素
	var oImg = document.createElement('img');
	document.body.appendChild(oImg);
	// 用一个数组保存需要加载的图片地址
	var imgArr = [
		'http://d.hiphotos.baidu.com/image/w%3D2048/sign=2846dc1369600c33f079d9c82e74500f/a044ad345982b2b7938726c333adcbef76099b98.jpg',
		'http://f.hiphotos.baidu.com/image/w%3D2048/sign=2da141ad013b5bb5bed727fe02ebd439/7dd98d1001e9390124aacd3879ec54e736d1960f.jpg',
		'http://g.hiphotos.baidu.com/image/w%3D2048/sign=5e067f12a918972ba33a07cad2f57b89/b8014a90f603738d27674f24b11bb051f819ec83.jpg',
		'http://f.hiphotos.baidu.com/image/w%3D2048/sign=fc9023a7d343ad4ba62e41c0b63a5baf/4bed2e738bd4b31ca2a24ab985d6277f9e2ff812.jpg',
		'http://f.hiphotos.baidu.com/image/w%3D2048/sign=99191869cebf6c81f7372be88806b035/9345d688d43f8794a159b42fd01b0ef41bd53a08.jpg',
		'http://a.hiphotos.baidu.com/image/w%3D2048/sign=4aaddd39718da9774e2f812b8469f919/8b13632762d0f70391b28bd60afa513d2697c5b3.jpg',
		'http://g.hiphotos.baidu.com/image/w%3D2048/sign=50df8b2efffaaf5184e386bfb86c95ee/fc1f4134970a304ebbdb83f2d3c8a786c9175c38.jpg'
	];
	// 这里是关键 new一个图片对象用来预加载图片
	var oImage = new Image();
	var iCur = 0;
	var i = 0;
	oImg.onclick = switchImg;
	// 切换图片
	function switchImg() {
		if (i >= imgArr.length) {
			i = 0;
		}
		oImg.src = imgArr[i];
		i++;
	}
	switchImg();
	// 图片预加载
	preload();
	function preload() {
		oImage.src = imgArr[iCur];
		oImage.onload = function() {
			iCur++;
			if (iCur < imgArr.length) {
				// 递归调用preload 使得oImage对象来加载数组中的图片
				arguments.callee();
			}
			document.title = iCur + '/' + imgArr.length;
		}

	}

}
```
其实原理就是这么简单，如果换成轮播图，也是一样的道理，一上来加载第一张图片后，后面的图片用**Image**对象进行预加载。
### 懒加载
大家平时都会用百度搜索一些图片，百度搜索的图片非常多，不可能一次性加入到页面上，假如有10000张图片，如果一次性加入到页面，那么打开页面估计浏览器直接崩溃掉了，这样用户体验很差，这个时候我们就需要图片**按需加载**，什么意思呢？只加载可视区的图片，可视区外的图片当用户滚动滚轴需要看的时候再加载，我们来做一个demo实现这样的功能。

比如页面结构如下如下：
```html
<ul id="ul1">
   	<li><img _src="images/1.jpg" src="images/white.JPG" /></li>
	...
</ul>
```
为了防止在IE下出现图片没有加载的标志，先用一张1*1像素的白色图片占位。**_src**是用来存取将要加载的图片地址。
JS的代码如下：
```javascript
window.onload = function() {
	var oUl = document.getElementById('ul1');
	var oImgs = oUl.getElementsByTagName('img');

	window.onscroll = showImg;

	showImg();
	function showImg() {
		// 获取滚动距离
		var scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
		// 轮询判断图片到顶部的距离
		for (var i = 0; i < oImgs.length; i++) {
			if (!oImgs[i].isLoad && getTop(oImgs[i]) < scrollTop + document.documentElement.clientHeight) {
				oImgs[i].src = oImgs[i].getAttribute('_src');
				// 加载过下次跳过了
				oImgs[i].isLoad = true;
			};
		};
	}
	// 获取图片到页面最上方的距离
	function getTop(obj) {
		var iTop = 0;
		while(obj) {
			iTop += obj.offsetTop;
			obj = obj.offsetParent;
		}
		return iTop;
	}
}
```
当用户乡下滚动的时候就会判断图片距离页面顶部的距离，如果距离顶部的距离小于可视区的高度那么就加载图片地址。

#### 推荐
如果你使用jquery的话推荐一个插件[jquery.lazyload.js](http://www.appelsiini.net/projects/lazyload)。它的用法非常简单，我就不再这里赘述了。
