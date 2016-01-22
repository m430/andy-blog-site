title: HTML5历史管理
date: 2016-01-19 14:03:04
categories: HTML5
tags: ['HTML5', 'History API']
---

### 为什么要做历史管理？
在很久以前，每个页面之间都是通过跳转来交互。浏览器会记录每次访问的url，但是后来**AJAX**横空出世，每次ajax的请求并不会使url改变，浏览器也无法记录这个状态，为了解决这个问题，W3C组织为HTML5新增了history API。
<!-- more -->
### HTML5 History API
HTML5提供的history API很简单：
- **history.pushState(data, title, url)**：把状态记录历史记录中。三个参数分别是数据，标题（所有浏览器都没有实现），地址（可选）
- **history.replaceState(data, title, url)**: 更改当前历史记录，参数同上。
- **window.onpopstate**事件：响应pushState和replaceState的调用。

> 注意：HTML5的history API必须运行服务器环境。

### 怎么应用History API
我们来做一个demo实现ajax无刷新更新的demo，html如下：
```html
<ul id="nav">
	<li><a href="/1">1</a></li>
	<li><a href="/2">2</a></li>
	<li><a href="/3">3</a></li>
</ul>
<div id="content"></div>
```
js代码如下：
```javascript
$('#nav a').click(function(event) {
	event.preventDefault();
	var _url = 'http://' + location.host + $(this).attr('href');
	var index = $('#nav a').index($(this));
	$.ajax({
		url: '/history.json',
		type: 'GET',
		dataType: 'json'
	})
	.done(function(data) {
		var obj = data[index];
		var html = '<p>' + obj.content + '</p>';
		var state = {
			url: _url,
			html: html
		};
		$('#content').html(html);
		history.pushState(state, '', _url);
	});
});

$(window).on('popstate', function(event) {
	if (event && event.originalEvent.state) {
		$('#content').html(event.originalEvent.state.html);
	};
});
```
json为模拟后台返回的数据：
```json
[{
	"content": "导航1的内容"
},
{
	"content": "导航2的内容"
},
{
	"content": "导航3的内容"
}]
```
我们在点击`a`链接的时候通过**history.pushState**把当前的“状态”保存到历史记录中，当用户用浏览器的前进后退按钮进行操作的时候会出发**popstate**事件，然后通过事件对象中拿到之前保存的“状态(state)”，页面并没有刷新就可以恢复之前的状态，增加了用户的体验。
