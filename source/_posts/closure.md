title: 深入浅出--闭包
date: 2016-01-14 10:57:41
categories: Javascript
tags: ['Javascript', 'closure']
---

### 什么是闭包？
> 闭包是指函数有自由独立的变量。换句话说，定义在闭包中的函数可以“记忆”它创建时候的环境。

说的很抽象是吗？让我们看一个闭包的例子，就很容易理解了
<!-- more -->
```javascript
function A() {
	var a = 'aaa';
	function B() {
		alert(a);
	}
	return B;
}

var b = A(); 
b() // 'aaa'
```
这是一段非常简单的代码，相信任何人都能看懂，我们运行这段程序发现一个奇怪的现象，我们在`b()`竟然弹出了`A`中的变量`a`，一般情况下，在Javascript世界中函数中声明的变量当函数执行完就随之消失，也就是说在这里`a`的作用域应该是`A`函数的作用域，但是在这个例子里我们看到一个有意思的现象，`A`执行后返回的`b`执行的时候竟然可以访问`A`里的变量`a`，这个谜题就是`A`函数变成了一个`闭包`。我们再回过头看看闭包的定义：**函数有自由独立的变量，闭包中的函数可以“记忆”它创建时候的环境**。

是的，不难看出在`A`函数中定义的变量`a`可以被外部作用域访问到，因为在`A`这个闭包中的函数`B`“记忆”了`A`的作用域环境，这就是神秘的`闭包`，我们终于揭开了它的面纱。
### 什么时候用闭包？
#### for循环轮询中
一个典型的例子就是在for循环中轮询索引的问题，我们经常会看到这样的代码：
```html
<p>1111111111111111111</p>
<p>2222222222222222222</p>
<p>3333333333333333333</p>
<p>4444444444444444444</p>
```
```javascript
function init() {
	var pEles = document.getElementByTag('p');
	for (int i = 0; i < pEles.length; i++) {
		pEles[i].onclick = function(){
			alert(i);
		}
	}
}
init();
```
乍一看没有什么错误，一般刚结束js的新手这么写觉得没什么问题，可是一运行代码发现，为什么每次弹出的都是`3`，见鬼了，这到底是什么原因呢？这段代码逻辑放到`Java`一点没有问题，问什么在JS中就不行了呢，百思不得其解，其实我们认真分析上面的代码，`init`函数执行后p元素的`onclick`回调并没有被执行，只是被定义了，这个时候for循环已经执行完了，`i`的值也变成元素的最后一个，每一个定义的`onclick`都是共享的一个环境，我们可以用`闭包`来解决这个问题，如下：
```javascript
function init() {
	var pEles = document.getElementByTag('p');
	for (int i = 0; i < pEles.length; i++) {
		pEles[i].onclick = (function(i){
			return function(){
				alert(i);
			}
		})(i);
	}
}
init();
```
我们将一个匿名函数定义的`闭包`赋值给了`onclick`，请注意，每一次轮询的过程中，都会产生一个新的作用域环境，在匿名函数定义的`闭包`中函数可以访问外部作用域的变量`i`，这个是匿名函数传参传进来的。这样我们就解决了上面引起的奇怪问题。
> 多说一句，在ES6中通过**let**关键字声明的变量不需要用闭包来解决，可以直接使用。

#### 用闭包模拟私有方法
在Java中我们通过关键字`public`和`private`可以轻松的设置类中的方法为公有还是私有，在JS的面向对象编程中，JS定义的对象一般是不存在私有变量和私有方法的，我们可以利用`闭包`的特性来实现这一特征。如下：
```javascript
var Counter = (function() {
	var count = 0;
	function change(x) {
		count += x;
	}
	return {
		add: function() {
			change(1);
		},
		sub: function() {
			change(-1)
		},
		value: function() {
			return count;
		}
	}
})();
```
我们可以看到通过闭包的形式模拟定义了一个`Counter`"类"，其中`change`为私有方法，`count`为私有变量，公共方法通过返回一个`匿名函数体`，外部作用域就可以通过调用Counter的这些方法来操作它的`私有变量count`。通过这种方式我们可以分离公共和隔离接口，这种方式在面向对象的编程中非常有用。
### 注意事项
`闭包`既然这么好，是不是可以随便用呢，当然不是这样，如果你不熟悉`闭包`还是尽量少用，不然你会创建出非常诡异的bug. 值得注意的是，当闭包被返回在外部调用时候，它所保存的父函数环境将一直存在于内存中，所以如果滥用闭包的结果可能导致性能问题，在IE下甚至会出现内存泄露。让我们看一个例子：

```javascript
function addHandler() {
	var el = document.getElementById('el');
	el.onclick = function() {
		el.style.backgroundColor = 'red';
	}
}
```
这一段最简单的绑定事件的代码非常容易看到，其实这里也产生了一个`闭包`，`addHandler`里面内嵌了`onclick指向的匿名函数`，然而在这句代码里产生了一个典型的`循环引用`的问题，Javascript的垃圾回收机制就无法回收javascript对象**指向onclick的匿名函数**和**本地对象el**，在javascript中，一个对象被引用一次，则它的`引用计数`会加1，反之解除引用则会减1，但是在这里我们出现了一个循环引用，他们就永远驻留在内存当中，除非你把浏览器关闭重启。如何解决这个问题呢，其实很简单，请看下面的代码：
```javascript
function addHandler() {
	var el = document.getElementById('el');
	el.onclick = function() {
		this.style.backgroundColor = 'red';
	}
}
```
我们不要使用`el`这个变量，用`this`就好了。
### 参考资料
1. [学习Javascript闭包（Closure）](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html) by 阮一峰。
2. [Javascript Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) on MDN
3. [Javascript Closure](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)  on MDN
4. [Closures Are Not Magic](http://stackoverflow.com/questions/111102/how-do-javascript-closures-work)  on StackOverFlow