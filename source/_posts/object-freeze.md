title: Javascript世界中的冻结大法Object.freeze
date: 2016-01-06 10:47:33
categories: Javascript
tags: Javascript
---

JS语言以灵活著称，你几乎可以做任何你想做的事情，当然它有时候带来的弊端也是让很懊恼，如果你不想让某个对象被改变，你需要不管什么时候看见它的时候它都像刚开始看见它的那样，而不是被人改的面目全非，所以你会想到把它冻结起来，很庆幸ES5提供了这个方法——`Object.freeze()`。

<!-- more -->
## Object.freeze
让我们看看ES5官方对它的解释：
> The `Object.freeze()` method freezes an object: that is, prevents new properties from being added to it; prevents existing properties from being removed; and prevents existing properties, or their enumerability, configurability, or writability, from being changed. In essence the object is made effectively immutable. The method returns the object being frozen.
> `Object.freeze()`方法可以冻结一个对象，防止新的属性被添加，防止存在的属性被删除，并且防止存在的属性的枚举性、配置性、可写性被更改。本质上使得它不可变，这个方法返回被冻结后的对象。

是的，官方文档已经说的很清楚了，为了更好的理解，我们举个例子来验证一下。
```javascript
var obj = {
	foo: 'aaa',
	xxx: {},
	prop: function() {
		console.log('prop func');
	}
}

// 没有冻结前，我们可以随意的修改obj的属性，随意的添加删除obj的属性
obj.bbb = 'bbb'; 
delete obj.prop;

// 冻结obj
Object.freeze(obj);

// 再做任何操作都是无效的
obj.ccc = 'ccc';
obj.foo = function() {
	console.log('foo func');
}
delete obj.bbb;

// 你会发现这个时候打印出来的跟冻结前的对象一模一样
console.log(obj);
```
值得一提的是，在`严格模式`下对冻结的对象进行修改或抛出`TypeError`。

## 深冻结DeepFreeze
但是obj现在是不是完全不能更改了呢，大家注意obj里有个属性`xxx`是一个对象。我们对它进行修改试试
```javascript
// 我们给xxx添加个属性
obj.xxx.aaa = 'aaa';
console.log(obj.xxx.aaa); // 'aaa'
```
竟然打印出来了，那是因为`Object.freeze`只能把对象进行`浅冻结`，如何对象的属性也是`object`这个属性则不会被冻结，那么我们如果想让他`深冻结`怎么办呢？可以这样
```javascript
// 定义一个深冻结的方法
function deepfreeze(obj) {
	var propNames = Object.getOwnPropertyNames(obj);
	propNames.forEach(function(name) {
		var prop = obj[name];
		if (typeof prop == 'object' && prop != null && !Object.isFrozen(prop)) {
			deepfreeze(prop)
		}
	});
	return Object.freeze(obj);
}

// 深冻结obj
deepfreeze(obj);
obj.xxx.aaa = 'aaa';
console.log(obj.xxx.aaa); // undefined
```
这次我们连对象里面的对象甚至更深层的对象都无法修改了，利用递归把是对象类型的属性全部冻结了。（完）

