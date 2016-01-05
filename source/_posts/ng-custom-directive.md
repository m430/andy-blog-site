title: AngularJS自定义指令
date: 2016-01-04 17:48:20
categories: AngularJS
tags: AngularJS
---

开门见山，我们直接从一个helloworld开始讲如何用angular去自定义一个指令
<!-- more -->
```javascript
angular.module('app')
.directive('helloWorld', function(){
	return {
		restrict: 'E',
		replace: true,
		template: '<div>Hello World</div>'
	}
})
```
在页面中这样调用
```html
<hello-world></hello-world>
```
然后你就可以看到输出了Hello World，很神奇吧，这就是angular的魅力所在，你可以自定义你想要的组件，去对html增强，这可以极大的重用我们的代码。
这里要解释几个参数：

| 参数|     备注|
| :-------- | :--------|
| restrict| **E**表示元素<br>**A**表示属性<br>**C**表示class方式<br>**M**表示注释，replace必须为true|
| replace| 默认为**false**表示不替换指令名，**true**为替换指令名|
| template| 表示模板，指向模板字符串和返回模板字符串的函数|
| templateUrl| 模板为外部模板时使用，表示模板的路径|
| scope|默认**false**每个指令作用域共享<br>**true**表示独立作用域<br>指向空对象**{}**表示隔离作用域，并且可以有三种绑定策略如下：<br>   `@` 绑定字符串<br> `=` 双向绑定外部scope的属性<br> `&` 绑定外部scope的函数 |
| controller| 指定指令的控制器|
| link| 一般在link函数中进行dom操作，它有四个参数如下：<br>`scope` 指令的作用域<br>`iElement` 指令最外层元素<br>`iAttrs`指令所有的属性<br>`ctrl`在指令嵌套的时候进行数据传递|
| transclude| 默认为**false**指令嵌套的时候必须设置为**true**，并且在内部用`ng-transclude`指令指出子指令替换的位置|

