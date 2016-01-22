title: 用Canvas画一个钟表
date: 2016-01-20 16:50:51
categories: HTML5
tags: ['HTML5', 'Canvas']
---


### 如何用Canvas画一个圆形
要想画一个钟表，我们先来看看如何画一个圆：


**ctx.arc(x, y, r, startRadian, endRadian, direction)**

- x:圆心x坐标
- y:圆心y坐标
- r:半径
- startRadian：起始弧度（弧度=角度*Math.PI/180）
- endRadian:结束弧度
- direction: true逆时针，默认false

<!-- more -->

### 第一步：画60份秒针的刻度
思路：先画60个圆弧，然后在上面覆盖一个白色圆，看代码：
```javascript
function drawSmallMark(x,y,r) {
	// 画60个等分弧
	ctx.beginPath();
	for (var i = 0; i < 60; i++) {
		ctx.moveTo(x,y);
		ctx.arc(x,y,r,6*i*Math.PI/180, 6*(i + 1)*Math.PI/180, false);
	};
	ctx.closePath();
	ctx.stroke();

	// 画个圆覆盖上面
	ctx.beginPath();
	ctx.fillStyle = 'white';		
	ctx.arc(x,y,r*19/20,0,360*Math.PI/180, false);	
	ctx.fill();
	ctx.closePath();
}
```
然后可以看到这样的图形：
![](img/clock1.jpg)

### 第二步：画12份大点的刻度
钟表上一般每5分钟的刻度会大一点，我们也用上面的思路再画一份，代码如下：
```javascript
function drawBigMark(x,y,r){
	// 画12个等分弧
	ctx.beginPath();
	ctx.lineWidth = 3;
	for (var i = 0; i < 12; i++) {
		ctx.moveTo(x,y);
		ctx.arc(x,y,r,30*i*Math.PI/180, 30*(i + 1)*Math.PI/180, false);
	};
	ctx.closePath();
	ctx.stroke();

	// 画个圆覆盖上面
	ctx.beginPath();
	ctx.fillStyle = 'white';		
	ctx.arc(x,y,r*18/20,0,360*Math.PI/180, false);	
	ctx.fill();
	ctx.closePath();
}
```
然后就变成这个样子：
![](img/clock2.jpg)
### 第三步：画指针
指针是重点，分为时针、分针、秒针，要根据时间去动态的更新指针的位置，看代码：
```javascript
function drawNeedle(x,y,r) {
	var date = new Date();
	var hours = date.getHours();
	var mins = date.getMinutes();
	var sens = date.getSeconds();
	// 获得当前时间所对应的弧度
	var hourValue = (-90 + hours*30 + mins/2) * Math.PI / 180;
	var minValue = (-90 + mins*6) * Math.PI/180;
	var senValue = (-90 + sens*6) * Math.PI/180;

	// 画时针
	ctx.lineWidth = 5;
	ctx.beginPath();
	ctx.moveTo(x,y);
	ctx.arc(x,y,r*10/20,hourValue,hourValue, false);	
	ctx.closePath();
	ctx.stroke();

	// 画分针
	ctx.lineWidth = 3;
	ctx.beginPath();
	ctx.moveTo(x,y);
	ctx.arc(x,y,r*14/20,minValue,minValue, false);	
	ctx.closePath();
	ctx.stroke();

	// 画秒针
	ctx.lineWidth = 1;
	ctx.beginPath();
	ctx.moveTo(x,y);
	ctx.arc(x,y,r*19/20,senValue,senValue, false);	
	ctx.closePath();
	ctx.stroke();
}
```
### 最后：设置定时器
然后我们设置一个定时器，每隔1秒重绘一次钟表，看起来就像是钟表动起来了。
```javascript
function drawClock() {
	var x = 200; // 圆心X坐标
	var y = 200; // 圆心Y坐标
	var r = 150; // 半径
	ctx.clearRect(0,0, oC.width, oC.height);
	drawSmallMark(x,y,r);
	drawBigMark(x,y,r);
	drawNeedle(x,y,r);
}
drawClock();
setInterval(drawClock, 1000);
```
这就是实现后的样子咯：

<canvas id="c1" width="400" height="400" style="background: white;"></canvas>

<script>
	window.onload = function() {
		var oC = document.querySelector('#c1');
		var ctx = oC.getContext('2d'); //获取画布的绘制环境，目前只有2d环境
		ctx.moveTo(200,200); // 设置起点

		function drawSmallMark(x,y,r) {
			// 画60个等分弧
			ctx.beginPath();
			for (var i = 0; i < 60; i++) {
				ctx.moveTo(x,y);
				ctx.arc(x,y,r,6*i*Math.PI/180, 6*(i + 1)*Math.PI/180, false);
			};
			ctx.closePath();
			ctx.stroke();

			// 画个圆覆盖上面
			ctx.beginPath();
			ctx.fillStyle = 'white';		
			ctx.arc(x,y,r*19/20,0,360*Math.PI/180, false);	
			ctx.fill();
			ctx.closePath();
		}
		function drawBigMark(x,y,r){
			// 画12个等分弧
			ctx.beginPath();
			ctx.lineWidth = 3;
			for (var i = 0; i < 12; i++) {
				ctx.moveTo(x,y);
				ctx.arc(x,y,r,30*i*Math.PI/180, 30*(i + 1)*Math.PI/180, false);
			};
			ctx.closePath();
			ctx.stroke();

			// 画个圆覆盖上面
			ctx.beginPath();
			ctx.fillStyle = 'white';		
			ctx.arc(x,y,r*18/20,0,360*Math.PI/180, false);	
			ctx.fill();
			ctx.closePath();
		}
		function drawNeedle(x,y,r) {


			var date = new Date();
			var hours = date.getHours();
			var mins = date.getMinutes();
			var sens = date.getSeconds();
			// 获得当前时间所对应的弧度
			var hourValue = (-90 + hours*30 + mins/2) * Math.PI / 180;
			var minValue = (-90 + mins*6) * Math.PI/180;
			var senValue = (-90 + sens*6) * Math.PI/180;

			// 画时针
			ctx.lineWidth = 5;
			ctx.beginPath();
			ctx.moveTo(x,y);
			ctx.arc(x,y,r*10/20,hourValue,hourValue, false);	
			ctx.closePath();
			ctx.stroke();

			// 画分针
			ctx.lineWidth = 3;
			ctx.beginPath();
			ctx.moveTo(x,y);
			ctx.arc(x,y,r*14/20,minValue,minValue, false);	
			ctx.closePath();
			ctx.stroke();

			// 画秒针
			ctx.lineWidth = 1;
			ctx.beginPath();
			ctx.moveTo(x,y);
			ctx.arc(x,y,r*19/20,senValue,senValue, false);	
			ctx.closePath();
			ctx.stroke();
		}
		function drawClock() {
			var x = 200; // 圆心X坐标
			var y = 200; // 圆心Y坐标
			var r = 150; // 半径
			ctx.clearRect(0,0, oC.width, oC.height);
			drawSmallMark(x,y,r);
			drawBigMark(x,y,r);
			drawNeedle(x,y,r);
		}
		drawClock();
		setInterval(drawClock, 1000);	
	}
</script>