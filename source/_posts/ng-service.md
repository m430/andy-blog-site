title: AngularJS服务详解
date: 2016-03-16 17:15:16
categories: AngularJS
tags: AngularJS
---

ng的服务（service）跟后端的服务概念其实是一样的，顾名思义就是提供统一的服务接口。服务就是对公共代码的抽象封装。在ng中我们通常用服务提供统一的接口，然后通过DI（依赖注入）的方式注入到`Controller`中进行使用。ng的服务分为几种类型，如下：
- 常量（Constant）：用于声明不会修改的值。
- 变量（Value）：用于声明会被修改的值，即变量。
- 服务（Service）：这个就是我们最常用的一种方式，通过service来声明一个服务，ng会new一个服务的实例，供注入使用。
- 工厂（Factory）：这个也是比较常用的一种方式，但是ng不会new它的实例，而是调用这个函数获取返回值，保存这个返回值供它到处注入使用。
- 供应商（Provider）：Provider比Factory更灵活，可以在ng的config方法中配置。

<!-- more -->

值得一提的是除了`Constant`外，其他服务都是通过`Provider`实现的。Constant和Value比较简单这里就不说了，重点讲后面三个有什么区别，分别在什么场景应用。

### Provider
Provider是最原始的一种用法，而且有些时候我们需要为某些服务定制一些特别的东西，就需要用Provider来实现了，声明方式如下：
```javascript
angular.module('app').provider('hello', function() {
	var name = 'World';
	this.setName = function(_name) {
		name = _name;
	};
	this.$get = function(/*这里可以放依赖注入的变量*/) {
		return 'Hello, ' + name;
	};
});
```
使用时：
```javascript
// 通过依赖注入注入到Controller中
angular.module('app').controller('SomeController', function($scope, hello) {
	// 这里hello会调用$get方法然后返回'Hello, World'字符串
	$scope.msg = hello;
});
```
如果要配置Provider就需要在ng的配置方法里设置:
```javascript
// 配置的时候是要对helloProvider进行配置
angular.module('app').config(function(helloProvider) {
	// 调用声明的setName设置name为Andy，则被注入的hello服务就返回的是Hello,Andy
	helloProvider.setName('Andy');
});
```
### Service
通常情况下，用的最多的就是Service了，它也更符合服务的语义，声明方式如下：
```javascript
angular.module('app').service('hello', function() {
	this.sayHello = function(name) {
		return 'Hello, ' + name;
	};
});
```
使用时：
```javascript
angular.module('app').controller('SomController', function($scope, hello) {
	// service的hello注入的其实是个实例
	$scope.msg = hello.sayHello('World');
});
```
### Factory
Factory定义通常如下：
```javascript
angular.module('app').factory('hello', function() {
	return 'Hello, World';
});
```
如上所说，它会直接把返回值给你。使用时也很简单：
```javascript
angular.module('app').controller('SomeController', function($scope, hello) {
	$scope.msg = hello;
});
```

### 应用场景
既然ng给我们提供了这么多中定义服务的类型，那么在什么情况下用什么类型来定义就成了问题了，通过总结可以得出如下结论：
- 需要全局可配置参数的服务用Provider
- 纯数据，没有行为用Value
- 只new一次实例，不用参数用Service
- 拿到函数，我们自己调用用Factory

> 注意：所有类型的服务只有Constant和Provider可以在ng的config阶段进行配置。

### 内置服务
ng本身为我们提供了很多非常实用的服务，这些服务需要我们熟练掌握，才能变成一个angular专家，现在我们来介绍一些常用的服务。

> 未完待续