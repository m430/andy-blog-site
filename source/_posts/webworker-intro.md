title: HTML5之WebWorkers
date: 2016-01-22 14:47:31
categories: HTML5
tags: ['HTML5', 'WebWorker']
---


### 什么是WebWorker？
大家都知道JS是单线程的，在页面执行JS脚本时候，页面状态是不可响应的，直到JS执行完成，**WebWorker**使得JS具有了多线程计算能力，在对页面无关的计算上，可以通过WebWorker在后台开启一个新的线程来完成，大大提高了性能。
### WebWorker运行环境
虽然WebWorker提供了多线程的编程能力，但是使用WebWorker还是存在一些限制，在这里做了罗列：

<!-- more -->

**可以访问**
- navigator: appName、appVersion、userAgent、platform
- location: 所有属性都是只读的
- self: 指向全局的worker对象
- 所有的ECMA对象，Object、Array、Date等
- XMLHttpRequest （可以发ajax请求）
- setTimeout()和setInterval()
- close方法： 立即停止worker线程
- importScript方法

也就是说，除了以上列出的这些，其余的操作都是不行的，比如操作DOM、alert、访问window、document等等。

### 如何使用WebWorker?
我们来做一个demo，测试一下计算数组在WebWorker中能够提升多少性能。为了对比我们先写一个常规的js数组计算：
```javascript
console.time(1);
var num= 0;
for (var i = 0; i < 100000000; i++) {
	num+=i;
}	
console.timeEnd(1);
```
我们累加循环1亿次，算出的时间是这样的：
![](img/worker1.jpg)

我去，竟然花费了780ms，这还只是简单的计算操作，如果更复杂的操作可想而知，下面重头戏来了，让我们用webworker来做个同样的测试：
```javascript
// UI线程
console.time(1);
var num = 0;
var w1 = new Worker('worker.js');
w1.postMessage(num);
w1.onmessage = function(event) {
	console.log(event.data);
}
console.timeEnd(1);

// worker.js 
function sum(num) {
	for (var i = 0; i < 100000000; i++) {
		num+=i;
	}
	return num;
}
// self指向w1
self.onmessage = function(event) {
	self.postMessage(sum(event.data));
}
```
值得一提的是，在JS线程和webworker线程之间要通过postMessage来传递数据，可以参考我之前写的一篇博客[HTML5跨文档通信](http://codinglife.in/2016/01/21/cross-document-communication/)。然后执行后的时间是：
![](img/worker2.jpg)

吓尿了有没有，**0.2ms**，足足提高了1000多倍的速度，当然并不是每一种计算都可以提升如此高的性能，经过测试，使用webworker可以提升的性能大概在几十倍~几百倍。

### 兼容性

目前**WebWorker**在IE10+都是支持的，可以看下图：
![](img/worker3.jpg)

如果不考虑10以下，还在等什么，webworker带你装逼带你飞啊。^_^