title: 初识Canvas
date: 2016-01-20 14:43:59
categories: HTML5
tags: ['HTML5', 'Canvas']
---
<style type="text/css">#c1,#c2 {background: #ccc;}</style>


### 什么是Canvas
> Canvas是用于在网页上绘图的技术。

<!-- more -->

#### 添加一个canvas画布
```html
<style>
#c1 {
	background: #ccc; //为了容易辨识设置个背景色
}
</style>
<canvas id="c1" width="400" height="400"></canvas> 
```
> 需要注意的是canvas不要通过css来设置画布的宽高，因为css设置的宽高是canvas默认宽高的等比例放大，而不是真正的宽高，通过元素宽高属性设置的才是真正的画布大小。

#### 绘制一个矩形
```javascript
var oC = document.querySelector('#c1');
var ctx = oC.getContext('2d'); //获取画布的绘制环境，目前只有2d环境
ctx.fillStyle = 'red'; // 设置填充色
ctx.strokeStyle = 'red'; // 设置边框色
ctx.lineWidth = 5; // 设置边框宽
ctx.lineJoin = 'round'; // 设置边框样式，默认为miter, round为圆角，bevel为斜角
ctx.fillRect(50,50,100,100); // 绘制一个实心矩形，参数为T（top）,L(left),W(width),H(height)
ctx.strokeRect(160,50,100,100); // 绘制一个线框矩形，参数同上
```
![](img/rect.jpg)
然后我们就可以看到一个canvas画出的方块了，很神奇有木有。canvas非常强大，让我们再来画几个玩意。
#### 画一个三角形
以前遇到一些不规则的图形，一般都是用图片来代替，现在我们可以通过canvas随意想画你想要的任何图形了。
```javascript
ctx.beginPath(); // 开始绘制路径
ctx.moveTo(50,50); // 绘制第一个坐标
ctx.lineTo(100,100); // 连接到第二个坐标
ctx.lineTo(200,100); 
ctx.closePath(); // 关闭终点和起点的连线
ctx.stroke(); // 用线框连接这些点，画实心的用ctx.fill()
```
然后就能看到西面这样子：
![](img/triangle.jpg)
#### 实现一个简单的画板
```javascript
oC.onmousedown = function(event) {
	var e = event || window.event;
	// 鼠标按下设置起点
	ctx.moveTo(e.clientX - oC.offsetLeft, e.clientY - oC.offsetTop);
	document.onmousemove = function(event) {
		var e = event || window.event;
		// 每移动一个点连接一个点
		ctx.lineTo(e.clientX - oC.offsetLeft, e.clientY - oC.offsetTop);
		ctx.stroke(); // 画线
	};
	document.onmouseup = function(event) {
		// 移除事件
		document.onmousemove = null;
		document.onmouseup = null;
	}
}
```
然后就可以得到一个画板，用你的鼠标来写个名字试试吧^_^：

<canvas id="c1" width="800" height="400"></canvas>

#### 实现方块移动的动画
```javascript
// 首先画一个方块
ctx.fillRect(0,0,100,100);
var num = 0;
// 设置一个定时器控制移动速度
setInterval(function() {
	if (num == oC.width) {
		num = 0;
	};
	num++;
	// 每次清除画布
	ctx.clearRect(0,0,oC.width, oC.height);
	// 重绘方块
	ctx.fillRect(num,num,100,100);
}, 30);
```
然后就像这样：

<canvas id="c2" width="300" height="300"></canvas>


<script type="text/javascript">
	window.onload = function() {
		var oC = document.querySelector('#c1');
		var ctx = oC.getContext('2d');
		oC.onmousedown = function(event) {
			var e = event || window.event;
			// 鼠标按下设置起点
			var oc_postion = oC.getBoundingClientRect();
			ctx.moveTo(e.clientX - oc_postion.left, e.clientY - oc_postion.top);
			document.onmousemove = function(event) {
				var e = event || window.event;
				// 每移动一个点连接一个点
				ctx.lineTo(e.clientX - oc_postion.left, e.clientY - oc_postion.top);
				ctx.stroke(); // 画线
			};
			document.onmouseup = function(event) {
				// 移除事件
				document.onmousemove = null;
				document.onmouseup = null;
			}
		};

		var oC2 = document.querySelector('#c2');
		var ctx2 = oC2.getContext('2d');
		// 首先画一个方块
		ctx2.fillRect(0,0,100,100);
		var num = 0;
		setInterval(function() {
			if (num == oC2.width) {
				num = 0;
			};
			num++;
			ctx2.clearRect(0,0,oC2.width, oC2.height);
			ctx2.fillRect(num,num,100,100);
		}, 30);
	}
</script>