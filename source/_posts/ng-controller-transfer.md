title: AngularJS之Controller的通信
date: 2016-03-04 18:12:39
categories: AngularJS
tags: AngularJS
---


在开发Angular应用的时候我们一般会按照具体业务模块划分Controller，这样便于我们的业务逻辑代码模块化管理，但是如何在不同的Controller之间进行通信成了一个需要解决的问题，Angular为我们提供了一种事件传播的机制。让我来详细介绍一下它是如何工作的。首先要明确在不同Controller之间通信分为两种情况：

- 具有父子嵌套关系的Controller
- 兄弟关系的Controller
<!-- more -->

同时要知道Angular提供给我们有三个非常有用的方法：
- $broadcast：广播事件，它会广播事件到所有子Controller中，也就是说只要子Controller绑定了目标事件就会被触发。
- $emit：发射事件，它会把目标事件发射到父级Controller中，父级Controller的目标事件被触发。
- $on：绑定事件，绑定目标事件。

这三个方法都是在Controller的`$scope`上，知道这三个方法后，分析上面的情况就简单多了。
### 父子嵌套关系
众所周知，父Controller如果要向子Controller传递数据，ng的`$scope`本来就具有继承特性。这种情况就不用多说了。
子Controller如何向父Controller通信呢？没错，就是通过`$emit`。直接看代码：
```html
<div ng-app="app" ng-controller="ParentCtrl">
	<span>{{name}}</span>
	<div ng-controller="ChildCtrl">
		<button ng-click="changeParent()">修改ParentCtrl里的数据</button>
	</div>
</div>
```
```javascript
angular.module('app', [])
.controller('ParentCtrl', function($scope) {
	// 先在父Controller里绑定目标事件
	$scope.$on('emitFromChildCtrl', function(event, msg) {
		// 传递的数据赋值给name
		$scope.name = msg;
	});
})
.controller('ChildCtrl', function($scope) {
	$scope.changeParent = function() {
		$scope.$emit('emitFromChildCtrl', 'ChildCtrl data');
	};
})
```

当点击按钮的时候`ChildCtrl`向`ParentCtrl`传递了数据`ChildCtrl data`，达到了我们的目的。: )
### 兄弟关系
如果出现下面这种DOM结构的话：
```html
<div ng-controller="Ctrl1">
	<button ng-click="changeCtrl2()">改变Ctrl2的数据</button>
</div>
<div ng-controller="Ctrl2">
{{name}}
</div>
```
兄弟关系如何来传递数据呢？既然`$broadcast`是可以向下级传递，`$emit`可以向上传递，`$emit`的事件流向看起来是没戏了，我们能不能通过父级向下冒泡传递到`Ctrl2`呢？答案是肯定的，我们知道ng给我们提供了一个`$rootScope`正好用上。上代码：
```javascript
angular.module('app', [])
.controller('Ctrl1', function($scope, $rootScope) {
	$scope.changeCtrl2 = function() {
		// 直接通过$rootScope绑定广播事件向下传递
		$rootScope.$broadcast('changeFromCtrl1', 'data from Ctrl1');
	};
})
.controller('Ctrl2', function($scope) {
	// 绑定目标事件
	$scope.$on('changeFromCtrl1', function(event, msg) {
		$scope.name = msg;
	});
})
```
这里直接利用`$rootScope`从顶层进行广播事件来传递数据。到此，Controller之间的传递方式讲完了。