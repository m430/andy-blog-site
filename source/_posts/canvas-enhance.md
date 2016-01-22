title: Canvas进阶之路
date: 2016-01-21 12:37:34
categories: HTML5
tags: ['HTML5', 'Canvas']
---

### 曲线
#### 一般曲线
我们如果想画一条曲线可以通过下面的方式：
```javascript
ctx.moveTo(100, 200);
// 参数：第一个控制点坐标（x1,y1）结束点坐标（endX,endY） 半径（r）
ctx.arcTo(100,100, 300,100,50);
ctx.stroke();
```
<!-- more -->
曲线如下图：
![](img/xian1.jpg)
#### 一阶贝塞尔曲线
```javascript
ctx.moveTo(100,200)
// 贝塞尔曲线 参数：第一组控制点（x1,y1） 结束点坐标（endX,endY）
ctx.quadraticCurveTo(100,100,200,100);
ctx.stroke();
```
曲线如下图：
![](img/xian2.jpg)
#### 二阶贝塞尔曲线
```javascript
ctx.moveTo(100,200)
// 贝塞尔曲线 参数： 第一个控制点（x1,y1） 第二个控制点(x2,y2) 结束点（endX,endY）
ctx.bezierCurveTo(100,100,200,200,200,100);
ctx.stroke();
```
曲线如下图：
![](img/xian3.jpg)

### 变换
canvas的变换其实跟CSS3的变换很相似，氛围偏移、旋转、伸缩等。
#### translate
```javascript
// 参数：偏移量（x轴，y轴）
ctx.translate(100,100);
ctx.fillRect(0,0,100,100);
```
然后画的方块分别向x轴和y轴偏移100px，如图:
![](img/bianhuan1.jpg)
#### rotate
```javascript
ctx.translate(100,100);
// 参数：旋转弧度
ctx.rotate(45*Math.PI/180);
ctx.fillRect(0,0,100,100);
```
旋转的度数是弧度而不是角度，需要转换，旋转后：
![](img/bianhuan2.jpg)
#### scale
```javascript
ctx.translate(100,100);
// 参数：宽比例，高比例
ctx.scale(0.5, 0.5);
ctx.rotate(45*Math.PI/180);
ctx.fillRect(0,0,100,100);
```
伸缩后：
![](img/bianhuan3.jpg)

### 图片
```javascript
var img = new Image();
img.src = 'test.jpg';
// 必须等到img加载后再进行canvas的操作
img.onload = function() {
	// drawImage(oImg, x, y, w, h) w宽和h高不写默认为图片本身的宽高
	ctx.drawImage(this, 0, 0);
	/*
	如果要以图片为背景平铺的话可以这样：
	var bg = ctx.createPattern(this, 'repeat');
	ctx.fillStyle = bg;
	ctx.fillRect(0,0,300,300); 然后会平铺到这个矩形中
	*/
}
```
### 渐变
#### 线性渐变
```javascript
var oC = document.querySelector('#c1');
var ctx = oC.getContext('2d');
// 线性渐变 参数L:起点坐标（x1,y1），终点坐标（x2,y2）
var obj = ctx.createLinearGradient(150,50,150,150);
obj.addColorStop(0, 'red'); //起点颜色
obj.addColorStop(1, 'blue');// 终点颜色 中间可以加多个点
ctx.fillStyle = obj; // 设置样式为渐变对象
ctx.fillRect(150,50,100,100);
```
然后可以看到这样的：
![](img/jianbian1.jpg)
#### 放射性渐变
```javascript
var oC = document.querySelector('#c1');
var ctx = oC.getContext('2d');
// 放射性渐变 参数：第一个圆坐标和半径(x1,y1,r1)，第二个圆坐标个半径(x2,y2,r2)
var obj = ctx.createRadialGradient(200,200,100,200,200,150);
// 添加渐变断点
ctx.addColorStop(0,'red');
ctx.addColorStop(0.5,'yellow');
ctx.addColorStop(1,'blue');
ctx.fillStyle = obj;
ctx.fillRect(0,0,oC.width,oC.height);
```
然后就可以看到这样的效果：
![](img/jianbian2.jpg)
### 文字
```javascript
ctx.font = '60px impact'; // impact是文字样式必须写
ctx.textAlign = 'left'; // 默认是left
ctx.textBaseline = 'top'; // 默认是alphabetic
var w = ctx.measureText('慕容展雲').width; //获取字体宽度
ctx.shadowOffsetX = 10; // 字体X轴阴影偏移量
ctx.shadowOffsetY = 10; // 字体Y轴阴影偏移量
ctx.shadowBlur = 3; // 阴影高斯模糊值
ctx.shadowColor = '#ccc'; // 阴影颜色
ctx.fillText('慕容展雲',0,0); // 写出来的是实心的字体 
// ctx.strokeText('慕容展雲',0,0);  虚线字体
```
然后可以看到这样：
![](img/ziti.jpg)