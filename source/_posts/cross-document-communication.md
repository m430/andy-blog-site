title: HTML5跨文档通信
date: 2016-01-21 16:38:40
categories: HTML5
tags: ['HTML5', 'postMessage']
---

### postMessage
窗口跨域操作的时候，我们可以通过**postMessage**来处理，比如我们的A窗口是在a.com域名下的，B窗口是在b.com域名下，我们在A窗口中用iframe包含一个B窗口，然后想通过点击A窗口的按钮来控制B窗口背景为红色。

<!-- more -->
```html
<body>
	<input id="btn" type="button" value="A窗口的按钮" />
	<iframe id="frame" src="http://www.b.com/b.html"></iframe>
</body>
```
我们在A窗口中通过postMessage来发送一个数据
```javascript
window.onload = function() {
	var oBtn = document.getElementById('btn');
	var oFrame = document.getElementById('frame');
	oBtn.onclick = function() {
		// 参数：第一个参数：数据，第二个参数：接收数据的域名（必须带协议）
		oFrame.cententWindow.postMessage('abc', 'http://www.b.com');
	}
}
```
然后在B窗口所在的域下添加这段接收的代码：
```javascript
window.addEventListener('message', function(ev) {
	if (ev.data == 'abc') {
		// 如果接收到的数据是abc则控制背景变红
		document.body.style.background = 'red';
	}
});
```
这样就实现了跨域窗口之间的消息通信，如果我们要限定可以发送消息的域名，可以在接收的域名下通过**event.origin**来判断接收的域名。