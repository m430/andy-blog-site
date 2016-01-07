title: Promise实战
date: 2016-01-05 17:40:07
categories: Javascript
tags: [Javascript, Promise, $q]
---

## Promise是什么？
要回答这个问题，我们首先要看一段代码，平时做开发的时候经常遇到这样的代码
<!-- more -->
```javascript
$.ajax({
	url: 'demo1.html',
	success: function(data1) {
		$.ajax({
			url: 'demo2.html',
			success: function(data2) {
				var total = data1.value + data2.value;
			}
		})
	}
})
```
在异步请求中，如果我们要对两个异步请求数据进行处理，就会出现上面的情况，嵌套回调（callback hell 回调地狱），试想如果我们有需求业务很复杂需要更多层的嵌套回调，那么这个代码将变的非常丑陋，难以维护。这个时候一些业界的大神忍不了了，于是发明了一个东东Promise来解决它，对，Promise其实就是用来解决异步编程嵌套回调问题的解决方案，目前，业界有很多标准，其中[Promise/A+](promises-aplus.github.io/promises-spec/)是目前认可度最高的也是应用最广的。

## Promise/A+
- Promise对象有三种状态：`Pending`对象初始状态，等待任务完成或被拒绝，`Fulfilled`任务完成状态，`Rejected`被拒绝的状态。
- Promise对象的状态只能从`Pending`转换到`Fulfilled`和`Rejected`，不能逆向转换。
- Promise对象必须有`then`方法，`then`方法必须返回一个Promise对象，并且同一个Promise对象可以注册多个`then`方法，调用顺序和注册顺序一致。
- `then`方法接受两个参数，成功的回调函数，失败的回调函数。
