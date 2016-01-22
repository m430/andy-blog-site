title: HTML5之WebSocket
date: 2016-01-21 17:07:35
categories: HTML5
tags: ['HTML5', 'WebSocket', 'Socket.io', 'Node.js']
---


### 什么是WebSocket？
> WebSocket 是HTML5一种新的通信协议。它实现了浏览器与服务器全双工通信(full-duplex)。

大家都知道传统的浏览器通信协议是**HTTP**，是一种基于客户端请求（request），服务端响应（response）的架构模型，但是这种通信协议有一个缺点就是数据的实时性很差，在做想聊天室、股票分析等Web应用的时候有点力不从心，很多时候为了实现数据的实时性，用的是**AJAX轮询**的方式，比如每隔1秒钟向服务端发送一次请求以获取最新的数据，这种方式也算是不得已而为之，“死马当活马医”了。这种方式会造成大量的带宽产生，性能也比较差。

<!-- more -->

### WebSocket横空出世
这个时候W3C小组为了解决这个问题坐不住了，他们制定了一套数据实时交互的协议，那就是**WebSocket**，它的底层其实也是一种TCP/IP的长连接协议, 只不过开始要借助HTTP协议来进行“握手”。

### Socket.io
WebSocket只是一种协议，目前比较火的实现就是基于node.js的Socket.io模块。下面我们来写一个demo看一下如何来应用websocket。
#### 环境搭建
- 安装node.js
- 安装git
- 安装socket.io
- 安装express

可以执行下面的命令来安装socket.io和express：
```bash
npm install socket.io express
```
这样环境就算是准备好了，下面开始正式写代码。
#### 服务端
首先在服务端我们需要创建一个server，通过socket.io来监听这个server，如果客户端发送出“握手”消息，则开启一个**双工通道**。
**server.js：**
```javascript
var express = require('express');
var app = express();
var server = require('http').Server(app);
var io = require('socket.io')(server);

server.listen(8888);

app.get('/', function(req, res) {
	res.sendfile(__dirname + '/index.html');
});

// 静态资源目录设置
app.use('/static', express.static(__dirname + '/static'));

// 绑定连接事件
io.on('connection', function(socket) {
	socket.emit('join', '欢迎');
});
```
#### 客户端
客户端我们需要通过socket.io提供的客户端库发送一个连接请求。
**index.html：**
```html
// 加载socket.io客户端文件
<script src="/static/socket.io.js"></script>
<script>
window.onload = function() {
	var oBtn = document.querySelector('#btn');
	oBtn.onclick = function() {
		var socket = io.connect('http://localhost:8888');
		socket.on('join', function(data) {
			alert(data);
		});
	};

}
</script>
<body>
	<input id="btn" type="button" value="进入聊天室">
</body>
```
当用户在点击`进入聊天室`按钮的时候，浏览器想服务端发送了一个http请求，服务端监听到`connection`事件的请求，“握手成功”，真正的打开了这个**双工通道**，客户端`socket`绑定了`join`事件，当连接成功后，服务端直接触发了`join`事件，返回`欢迎`字符串，注意，此时，是服务端主动向客户端触发了`join`事件。

#### 广播事件
socket.io还提供了服务端向客户端广播事件的API，比如我们在需要在聊天室应用中，当一个新人进来后，我们要向其他人广播一个事件告诉他们有人进来了。
```javascript
// 客户端代码
window.onload = function() {
	var oBtn = document.querySelector('#btn');
	oBtn.onclick = function() {
		var socket = io.connect('http://localhost:8888');
		socket.on('join', function(data) {
			alert(data);
		});
		socket.on('listenJoin', function(data) {
			alert(data);
		});
	};
};

//服务端
socket.broadcase.emit('listenJoin', '有新人进来了');
```
当用户点击第一次进入聊天室的时候，会弹窗提示欢迎，然后会提示其他已经在聊天室的用户**有新人进来了**。如下图：
![](img/broadcast.jpg)

> 值得一提的是socket.io在浏览器兼容性方面做出了巨大的努力，websocket目前在IE10+都是支持的，可以通过[caniuse](http://caniuse.com/#search=websocket)观看详细信息，就算在不支持的情况下，socket.io也进行了降级处理，采用了传统的Ajax polling的方式。
