title: Flux入门教程
date: 2016-02-01 18:01:56
categories: React
tags: [Flux, React, 架构, Webpack]
---


### 为什么要用Flux？（Why）
我不想为了构建一个简单的web应用去引出各种复杂的概念，但是我们要明确的是当在生产环境中，随着业务需求的不断复杂和变多，如何用一种优雅的方式管理和维护我们的前端代码，这将变成了一个严峻的问题，通常我们会想到MVC（Model-View-Controller），是的，没错，这是一种很流行的分层架构，而且已经在后端（server side）中运用了几十年，这很好，但是当你的业务变的越发复杂的时候，你的架构中的不同分层之间的数据流向可能会变的错从复杂，当出现一个诡异的bug时候你很难从这些复杂的关系中找到一些“蛛丝马迹”。今天我们来介绍一个新家伙，它的名字叫做`Flux`。

<!-- more -->

### 什么是Flux？（What）

`Flux`是由Facebook出品的，Facebook推出了React这个库虽然提供了一种优雅的方式去编写组件，但是如果构建一个大型的web应用程序，react还远远不够，因为它只是一个**view**，`Flux`提供了一种**单向数据流**的架构模式，你非常容易看到应用程序的数据流向，就像下面这样：

`Flux`架构中包含了Action（动作）、Dispatcher（调度者）、Store（仓库）、View（视图），其中View一般是React实现的组件（当然也不一定非要用react），从上图中我们也可以看到一般情况下Action是用户在View交互中产生的，比如点击某个按钮，所有的Action都是通过Dispatcher来分配更新哪个Store中的状态（State），状态更新后Store会触发(emit)视图上的改变事件，然后更新View，数据的流向非常清晰，我们很容易推断应用是如何工作的。

### 怎么用Flux? （How）
说的这么好的架构，具体怎么应用呢？没有实践就是纸上谈兵，我们直接来做一个TodoApp的Demo，直接用我们上一节搭建好的开发环境，没有看的小伙伴请出门左转[利用Webpack搭建React开发环境](http://codinglife.in/2016/01/28/webpack-react/)。然后首先我们先安装好需要添加的库（Flux、lodash）。
```bash
npm install flux lodash --save
```
#### Step1. 分析组件构成，定义组件静态模板
我们要做一个应用或者一个website的时候首先要从大局出发看看这个应用是由哪些组件构成的，然后把它拆分成一小块一小块的，这样做起来思路会变的比较清晰，也就是一种组件化开发的思维方式，下面贴出我们要做的TodoApp的解构图：

![img](img/todoApp.jpg)

大致的组件结构就出来了：

```javascript
- TodoApp
	- TodoHeader
		- TodoTextInput
	- TodoList
		- TodoItem
	- TodoFooter
```

然后就很清晰了有木有，我们先把项目的架构搭起来如下图：
```bash
.
│   index.html
│   webpack.config.js               #webpack配置文件
│
├───css
│       app.css
│
├───img
│       bg.png
│
└───js
    │   app.js                      #项目入口文件
    │
    ├───actions                     #Action目录
    │       TodoActions.js
    │
    ├───components                  #组件目录
    │       TodoApp.js              #主组件
    │       TodoFooter.js           #footer
    │       TodoHeader.js           #header
    │       TodoItem.js             #item
    │       TodoList.js             #itemlist
    │       TodoTextInput.js        #input
    │
    ├───constants                   #常量
    │       TodoConstants.js
    │
    ├───dispatcher                  #调度者
    │       AppDispatcher.js
    │
    └───stores                      #仓库
            TodoStore.js

```
然后分别定义组件的静态模板如下：
**TodoApp.js:**
```javascript
import React from 'react';
import TodoHeader from './TodoHeader';
import TodoFooter from './TodoFooter';
import TodoList from './TodoList';
import TodoStore from '../stores/TodoStore';

function getTodoState() {
    return {
        allTodos: TodoStore.getAll()
    }
}

require('../../css/app.css');

class TodoApp extends React.Component {
    constructor() {
        super();
        this.state = getTodoState();
    }
    componentDidMount() {
        TodoStore.addChangeListener(this._onChange);
    }
    componentWillUnmount() {
        TodoStore.removeChangeListener(this._onChange);
    }
    _onChange() {
        this.setState(getTodoState());
    }
    render() {
        return (
          <div>
              <TodoHeader />
              <TodoList allTodos={this.state.allTodos} />
              <TodoFooter allTodos={this.state.allTodos} />
          </div>
        );
    }
}

export default TodoApp;
```
**TodoHeader.js:**
```javascript
import React from 'react';
import TodoTextInput from './TodoTextInput';

class TodoHeader extends React.Component {
    render() {
        return (
            <header id="header">
                <h1>Todos</h1>
                <TodoTextInput id="new-todo" placeholder="What need to be done?" />
            </header>
        );
    }
}

export default TodoHeader;
```
**TodoFooter.js:**
```javascript
import React from 'react';

class TodoFooter extends React.Component {
    render() {
        var itemLeft = this.props.allTodos.length;
        return (
            <footer id="footer">
                <span id="todo-count">
                    <strong>{itemLeft}</strong>
                </span>
            </footer>
        );
    }
}

export default TodoFooter;
```
**TodoList.js:**
```javascript
import React from 'react';
import TodoItem from './TodoItem';

class TodoList extends React.Component {
    render() {
        var todos = [];
        if (this.props.allTodos.length > 0) {
            this.props.allTodos.map((item) => {
                todos.push(<TodoItem key={item.id} value={item.text} />);
            });
        }
        return (
            <section id="main">
                <input id="toggle-all" type="checkbox" />
                <label htmlFor="toggle-all">Mark all as complete</label>
                <ul id="todo-list">
                    {todos}
                </ul>
            </section>
        );
    }
}

export default TodoList;
```
**TodoItem.js:**
```javascript
import React from 'react';
import TodoItem from './TodoItem';

class TodoList extends React.Component {
    render() {
        var todos = [];
        if (this.props.allTodos.length > 0) {
            this.props.allTodos.map((item) => {
                todos.push(<TodoItem key={item.id} value={item.text} />);
            });
        }
        return (
            <section id="main">
                <input id="toggle-all" type="checkbox" />
                <label htmlFor="toggle-all">Mark all as complete</label>
                <ul id="todo-list">
                    {todos}
                </ul>
            </section>
        );
    }
}

export default TodoList;
```
**TodoTextInput:**
```javascript
import React from 'react';

class TodoTextInput extends React.Component {
    render() {
        return (
            <input className={this.props.className}
                   id={this.props.id}
                   placeholder={this.props.placeholder} />
        );
    }
}

export default TodoTextInput;
```
然后用webpack编译后会看到下面这样：

![img](img/todoApp1.jpg)

静态模板写完了，下一步我们来为静态模板添加交互事件，让数据流动起来。
#### Step2. 添加交互事件
首先，我们不着急给这个静态应用添加事件，先来分析一下在这个应用中那些数据是状态`State`，那些又是从父级`props`传递下来的数据，这个是至关重要的，我们先来想一个问题`那些组件应该有State?`，通常情况下大部分的组件工作应该是从父级`props`取数据并渲染出来，但是，**有用户输入、服务器请求，或时间变化等作出相应，这时才需要用State**，我们应该**尝试把尽可能多的组件无状态化**，这样做能隔离State，把它放在最合适的地方，也能减少冗余，这样也易于解释程序是如何工作的。

好，既然是像上面所说，我们这个TodoDemo中那些是State呢？

- TodoTextInput输入的内容
- 每条Item是否可编辑的状态

我们要实现的demo中肯定有一个是添加item的事件，所以我们在`TodoTextInput`组件中添加如下：
```javascript
import React from 'react';
import TodoActions from '../actions/TodoActions';

const ENTER_KEY_CODE = 13;

class TodoTextInput extends React.Component {
	constructor() {
		super();
		this.state = {
			value: ''
		}
	}
	_save() {
        TodoActions.createItem(this.state.value);
        this.setState({value: ''});
    }
    // 当输入改变的时候value要跟着变
    _onChange(event) {
        this.setState({value: event.target.value});
    }
    _onKeyDown(event) {
        if (event.keyCode == ENTER_KEY_CODE) {
            this._save();
        }
    }
    render() {
        return (
            <input className={this.props.className}
                   id={this.props.id}
                   placeholder={this.props.placeholder}
                   value={this.state.value}
                   onBlur={this._save.bind(this)}
                   onChange={this._onChange.bind(this)}
                   onKeyDown={this._onKeyDown.bind(this)} />
        );
    }
}

export default TodoTextInput;
```
#### Step3. 创建Action
上面`TodoActions.createItem()`方法就是我们需要创建的一个action，如下：
```javascript
import AppDispatcher from '../dispatcher/AppDispatcher';
import TodoConstants from '../constants/TodoConstants';

class TodoActions {
    createItem(text) {
        AppDispatcher.dispatch({
            actionType: TodoConstants.TODO_CREATE,
            text: text
        });
    }
}

export default new TodoActions();
```
然后这个action中我们看到了通过`AppDispatcher.dispatch()`进行分发action。
#### Step4. 创建Store，注册Action
然后我们需要创建一个Store来更新数据，`Flux`中在Store中通过`Dispatcher`注册Action来决定更新那部分数据。如下：
```javascript
import {EventEmitter} from 'events';
import _ from 'lodash';
import AppDispatcher from '../dispatcher/AppDispatcher';
import TodoConstants from '../constants/TodoConstants';

const CHANGE_EVENT = 'change';

var _todos = [];

function createItem(text) {
    var id = (+new Date() + Math.floor(Math.random() * 999999)).toString(36);
    _todos.push({
        id: id,
        complete: false,
        text: text
    });
}

class TodoStore extends EventEmitter{
    getAll() {
        return _todos;
    }
    addChangeListener(callback) {
        this.on(CHANGE_EVENT, callback);
    }
    removeChangeListener(callback) {
        this.removeListener(CHANGE_EVENT, callback);
    }
    emitChange() {
        this.emit(CHANGE_EVENT);
    }
}

let todoStore = new TodoStore();
// 注册Action
AppDispatcher.register((action) => {
    switch (action.actionType) {
        case TodoConstants.TODO_CREATE:
            if (action.text.trim() !== ''){
                createItem(action.text);
                todoStore.emitChange();
            }
            break;
        default:
    }
});

export default todoStore;
```
`Dispatcher`通过`actionType`知道要执行对应的逻辑来更新组件状态，更新后通过`EventEmitter`的`emit`来触发在`TodoApp`上监听的事件，从而更新了组件状态和表现。整个流程非常清晰，一气呵成的感觉，从View（用户操作）--> Action --> Dispatcher（分发）--> Store （更新状态）--> View（视图更新）。
还需要添加别的交互，比如删除Item等操作完全可以按照这个流程进行，非常方便。这些操作我就不在这里赘述了，可以直接看[源码](https://github.com/ShadowZheng/react-demos/tree/master/demo3)。

### 小结
`React`为更好的实践**组件化开发**提供了很好的思路，结合独有的JSX语法可以写出**高内聚**的组件，html，js，css被整合在一起了，我们看待整个应用的视角发生了改变，不再是以前那种散开的分布，而是更加聚合的一种视角变化。我们关注点只需要放在组件上以及整合组件（组件嵌套或者通信）上。但是如果去构建一个大型的Web应用React还不够，Facebook为我们提供了一种新的架构模式`Flux`，这是一种单向数据流的架构模式，为我们开发大型Web应用提供了很好的实践，但目前Flux的发展比较碎片化，因为Flux开启了一个新的思路，它并不是一个固定的框架，目前比较流行的实现有`Redux`、`Reflux`等等。其中以`Redux`关注最多，后续我也会在这篇Demo的基础上做一些实践。（完）