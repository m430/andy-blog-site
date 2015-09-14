title: AngularJS系列之UI-Router
date: 2015-08-11 09:27:06
categories: AngularJS
tags:
---

**Angular**自带的**ngRoute**已经实现的很好，但是也有它局限性，它不能实现路由嵌套，简单点说就是如果你想在模板内套用**ng-view**是不可以的。还好强大的社区为我们提供了新的路由，那就是——**UI-Router**。

下面我会分别介绍**ngRoute**以及**UI-Router**的用法和各自的优缺点。

## ngRoute

> **ngRoute**是Angular自带的路由模块，在1.2版本后已经独立出去，需要单独引用。

<!-- more-->

### ngRoute一般配置用法

首先，引用文件和依赖：

``` javascript
<script src="angular.min.js"></script>
<script src="angular-route.min.js"></script>
```
然后定义模块并注入**ngRoute**模块，配置路由如下：

``` javascript
angular.module('app', ['ngRoute']).config(['$routeProvider', function($routeProvider) {
	$routeProvider.when('/view1', {
		templateUrl: 'tpl/view1.html',
		controller: 'View1Ctrl'
	}).when('/view2', {
		template: 'tpl/view2.html',
		controller: 'View2Ctrl'
	}).otherwise({redirectTo: '/view1'});
}]);
```

最后通过**ng-view**显示匹配的路由内容:

``` html
<ul>
	<li><a href="#/view1">View1</a></li>
	<li><a href="#/view2">View2</a></li>
</ul>
<div ng-view></div>
```
当点击view1链接时候，Angular将根据url在路由表中匹配，找到后会把对应的view1模板填充到带有**ng-view**指令的区域，同样点击view2的链接时候也是一样的。这里定义路由同时指定了控制器，我们也可以为同一个模板指定不同的控制器。

### 匹配灵活的路由

通常有一种业务场景，比如一个用户管理系统，列表页来展示用户，点击每个用户跳转到对应的详细信息页面，详细信息页就是一个模板，而且是可以共用的模板，用**Angular**的**ngRoute**怎么实现呢？这里就需要灵活的进行配置路由了。我们给路由的URL加上参数，如下：

``` js
angular.module('app', ['ngRoute']).config(['$routeProvider', function($routeProvider) {
	$routeProvider.when('/uses/:userId', {
		templateUrl: 'tpl/editUser.html',
		controller: 'EditUserCtrl'
	})
}]);
```
这里发现我们用了一个**:userId**在URL中，这是种通配符的写法，当用户点击每个用户的时候，URL会自动拼接目标用户的id。跳转到用户信息页面，但是此时页面数据并没有拿到，页面是先渲染出来后拿到数据，当**Angular**发现数据更新了，页面会再渲染一次，页面可能会出现难看的抖动，这时我们可能希望在渲染页面前就拿到数据，当渲染后页面就已经是填充好数据的。**ngRoute**为我们提供了**resolve**属性，配置如下：

``` javascript
angular.module('app', ['ngRoute']).config(['$routeProvider', function($routeProvider) {
	$routeProvider.when('/uses/:userId', {
		templateUrl: 'tpl/editUser.html',
		controller: 'EditUserCtrl',
		resolve: {
			user: function($route, Users) {
				return Users.getById($route.current.params.userId);
			}
		}
	})
}]);
```
这里**$route**和**Users**服务会被注入用于获取用户数据。当**resolve**属性处理完毕后，他们将会被注入到路由的控制器里：
``` javascript
.controller('EditUserCtrl', ['$scope', 'user', function($scope, user){
	$scope.user = user;
	...
}]);
```
到这里**ngRoute**的使用基本介绍完了，使用还是很简单的，但是它不能嵌套的局限性使得开发大型复杂的Web应用变得困难。

## UI-Router

> UI-Router是**Angular**社区的大神们推出了一套更好的路由模块，实现了嵌套路由机制。

### UI-Router管理状态

UI-Router的工作原理非常类似于ngRoute，不同的是它只关注状态。

- 在应用程序的整个用户界面和导航中，一个状态对应于一个页面位置。
- 通过定义`controller`、`template`和`view`等属性，来定义指定位置的用户界面和界面行为。
- 通过嵌套的方式来解决页面中的一些重复出现的部位。

状态被激活时，它的模板会自动插入到父状态对应的模板中包含`ui-view`属性的元素内部。如果是顶层的状态，那么它的父模板就是`index.html`。

#### 激活状态

有三种方法来激活状态：

- 调用`$state.go()`方法，这是一个高级的便利方法。
- 点击包含`ui-sref`指令的链接。
- 导航到与状态相关的`url`。

#### Template模板配置

模板一般有三种配置方法。
**方法一：**配置`template`属性，指定一段HTML作为模板。

``` javascript
$stateProvider.state('contacts', {
	template: '<h1>My Contacts</h1>'
})
```

**方法二：**指定`templateUrl`来指定一个template文件。**这种是最常用的方法。**
``` javascript
$stateProvider.state('contacts', {
	templateUrl: 'contacts.html'
})
```
`templateUrl`也可以是函数返回的url，通过参数`$stateParams`来拼接。
``` javascript
$stateProvider.state('contacts', {
	templateUrl: function($stateParams) {
		return 'contacts.' + $stateParams.contactId + '.html';
	}
})
```
**方法三：**通过`templateProvider`函数返回模板html。
``` javascript
$stateProvider.state('contacts', {
  templateProvider: function ($timeout, $stateParams) {
    return $timeout(function () {
      return '<h1>' + $stateParams.contactId + '</h1>'
    }, 100);
  }
})
```
如果你想在状态被激活前有一些默认的内容，可以这么做：
``` html
<body>
    <ui-view>
        <i>Some content will load here!</i>
    </ui-view>
</body>
```

#### Controller配置方法

可以为模板设置一个控制器：
``` javascript
$stateProvider.state('contacts', {
  template: '<h1>{{title}}</h1>',
  controller: function($scope){
    $scope.title = 'My Contacts';
  }
})
```
如果在模块中已经定义好了控制器，直接写控制器的名称就可以：
``` javascript
$stateProvider.state('contacts', {
  template: '<h1>{{title}}</h1>',
  controller: 'ContactsCtrl'
})
```
如果需要动态的返回控制器，可以这样配置：
``` javascript
$stateProvider.state('contacts', {
  template: ...,
  controllerProvider: function($stateParams) {
      var ctrlName = $stateParams.type + "Controller";
      return ctrlName;
  }
})
```
控制器可以用`$state.on()`方法监听事件状态转换。

#### 状态改变事件
所有的这些状态都是在`$rootScope`作用域触发。
- **$stateChangeStart：**当模板开始解析前触发。

``` javascript
$rootScope.on('$stateChangeStart', function(event, toState, toParams, fromState, fromParams){
	...
});
```
> **注意：**`event.preventDefault()`可以阻止模板解析。
- **$stateNotFound：**当状态无法找到时触发。
- **$stateChangeSuccess：**当模板解析完成后触发。
- **$stateChangeError：**当模板解析过程有错触发。

使用方法如`$stateChangeStart`。

#### 视图加载事件
- **$viewContentLoading：**当视图开始加载，Dom渲染完成之前触发。

``` Javascript
$scope.$on('$viewContentLoading', 
function(event, viewConfig){ 
    // Access to all the view config properties.
    // and one special property 'targetView'
    // viewConfig.targetView 
});
```

- **$viewContentLoaded：**当视图加载完成，Dom渲染完成之后触发，使用同上。

### 多视图嵌套

UI-Router相比ngRoute的优势就在于可以多视图嵌套，如果需要动态填充页面上的多个区域，用ngRoute就不可以了（可以用ng-include），用UI-Router实现就很方便了。

![enter image description here](http://bubkoo.qiniudn.com/MultipleNamedViewsExample.png)

如上图，如果我们希望同时更新`fileters`、`tabledata`、`graph`三个区域的视图。可以为每个视图用`ui-view`指令，并且为他们分别取一个名字。

``` html
<!-- index.html -->
<body>
  <div ui-view="filters"></div>
  <div ui-view="tabledata"></div>
  <div ui-view="graph"></div>
</body>
```

这样管理状态可以根据视图分别进行管理：

``` Javascript
$stateProvider
  .state('report',{
    views: {
      'filters': {
        templateUrl: 'report-filters.html',
        controller: function($scope){ ... controller stuff just for filters view ... }
      },
      'tabledata': {
        templateUrl: 'report-table.html',
        controller: function($scope){ ... controller stuff just for tabledata view ... }
      },
      'graph': {
        templateUrl: 'report-graph.html',
        controller: function($scope){ ... controller stuff just for graph view ... }
      },
    }
  })
```
可以为每个视图分别设置`templateUrl`和`controller`。

在 ui-router 内部，views属性中的每个视图都被按照viewname@statename的方式分配为绝对名称，viewname是目标模板中的ui-view对应的名称，statename是状态的名称，状态名称对应于一个目标模板。@前面部分为空表示未命名的ui-view，@后面为空则表示相对于根模板，通常是 index.html。

> 至此，Angular的路由模块基本讲完了，花了半天时间把这两天学习的整理了一下，后续会不断更新Angular系列的文章，如果你喜欢我的文章，欢迎分享和留言。

##### 参考资料
[bubkoo 学习 ui-router 系列文章](http://bubkoo.com/2014/01/02/angular/ui-router/guide/index/)
[UI-Router document](http://angular-ui.github.io/ui-router/site/#/api/ui.router)
