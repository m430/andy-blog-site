title: AngularJS指令详解
date: 2016-03-16 10:35:05
categories: AngularJS
tags: AngularJS
---


ng中指令（directive）是一个非常重要的概念，你可以用它自定义HTML元素，在Angular官方文档中称它为HTML语言的DSL拓展。按照指令的使用场景和作用可以分为：
- 组件型指令（Component）
- 装饰器型指令（Decorator）

<!-- more -->

### 组件型指令
> **定义：**组件型指令也就是我们通常所说的组件，它是一个小型的、自封装和内聚的一个整体，它包含业务所需要显示的视图和交互逻辑。
> **作用：**组件型指令主要是为了将复杂而庞大的View分离，使得页面的View具有更强的可读性和维护性。同时某些情况也增加了代码的复用性。

组件型指令应该满足封装的“高内聚低耦合”的特性，它的`scope`应该是独立的（isolated），不需要对父作用域有任何依赖，不然父作用域改变可能它也要跟着改变，这种封装是很脆弱的。组件型指令通常的定义方式应该是这样：

```javascript
angular.module('app').directive('componentDirective', function() {
	return {
		// 可以用作HTML元素也可以用做HTML属性
		restrict: 'EA',
		// 使用独立作用域
		scope: {
			configure: '=', //双向绑定的变量
			name: '@', // 绑定字面量
			change: '&' // 绑定函数
		}，
		// 指定模板
		templateUrl: 'components/configure/comDirective.html',
		// 声明指令控制器
		controller: function($scope) {
			...
		}
	}
});
```

对于组件型指令，更重要的是内容信息的展示，所以我们一般不涉及指令的`link`函数，尽量将业务逻辑放置在`controller`中。组件型指令给我们提供了一种组件化开发模式，这也是前端开发中一直追求的理想目标，试想一下，如果我们构建一套基础组件库，那么搭建一个完整的site就像是小孩搭积木一样简单，可以很快的构建出一个产品原型，想想还有点小激动有木有？

### 装饰器型指令

> **定义：**装饰器型指令顾名思义就像是“装饰”一下附加在元素上，用来增强元素功能。
> **作用：**如上所说，装饰器型的指令主要用于添加行为和保持View和Model同步。

装饰器型指令并不是内容主体，而是附加的能力，就像“插件”一样，随用随插，比如我们定义一个`draggable`装饰器指令放到任何元素上面，那么这个元素就变成可拖拽的了，通常由于一个元素（包括上面定义的组件型指令）可能被用上多个装饰器指令，所以装饰器指令通常不用新作用域或独立作用域。那么在装饰器指令中如何访问绑定属性呢？请看下面：
- 对于`@`型的绑定，我们可以直接通过**attrs.propertyName**获取
- 对于`=`型双向数据绑定，我们可以通过**scope.$eval(attrs.propertyName)**获取，获取到的数据同样具备双向绑定能力。
- 对于`&`型绑定的函数，这个比较特殊，本质上这个函数还是绑定在`scope`上，我们同样可以通过**scope.$eval(attrs.eventName, {arg: value})**的方式，其中后面的对象是要给`eventName`这个函数进行传参，这种应用方式尤为重要。

定义装饰器型的指令通常的写法如下：
```javascript
angular.module('app').directive('decoratorDirective', function() {
	return {
		// 装饰器指令一般用作属性
		restrict: 'A',
		link: function(scope, element, attrs) {
			...
		}
	}
});
```

### 小结
对于组件型指令和装饰器型指令区分非常重要，他们在写法、业务含义等方面有非常明显的区别，理解他们，对于我们日常指令开发具有很好的指导作用。（完）

### 参考
1. 《AngularJS 深度剖析与最佳实践》 by 雪狼 & 破狼。
2. [AngularJS官方指南](https://angularjs.org)
