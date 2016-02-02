title: 利用Webpack搭建React开发环境
date: 2016-01-28 16:08:25
categories: React
tags: [React, Webpack]
---

以前一直用grunt和gulp来构建前端开发环境，学习React后听说了Webpack的大名，所以又让我好好折腾了一把。体验后一个字就是**好**！还是让我们从一个**HelloWorld**开始吧。

### 准备
首先我们要安装好如下软件
- node.js (最好4.0以后)
- git 

<!-- more -->

### 开始
然后通过`npm init helloworld`初始化一个项目，请按照如下修改`package.json`添加依赖包:
```json
{
  "name": "react-webpack-es6-seed",
  "version": "1.0.0",
  "description": "",
  "main": "main.js",
  "dependencies": {
    "babel-core": "^6.4.5", 
    "babel-loader": "^6.2.1",
    "babel-preset-es2015": "^6.3.13",
    "babel-preset-react": "^6.3.13",
    "css-loader": "^0.23.1",
    "jsx-loader": "^0.13.2",
    "react": "^0.14.6",
    "react-dom": "^0.14.6",
    "style-loader": "^0.13.0",
    "webpack": "^1.12.12",
	"webpack-dev-server": "^1.14.1"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```
然后到项目目录下执行
```bash
npm install
```
待所有依赖包都下好后，创建`index.html`代码如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>react webpack environment</title>
</head>
<body>
	<div id="content"></div>
	<script src="bundle.js"></script>
</body>
</html>
```
然后我们创建一个`main.js`代码如下：
```javascript
import React from 'react';
import ReactDOM from 'react-dom';

require('./main.css');

class HelloWorld extends React.Component {
    render(){
        return <h1>Hello World!</h1>;
    }
}

ReactDOM.render(
    <HelloWorld />,
    document.getElementById('content')
);
```
然后再创建一个`main.css`：
```css
body {
	background: yellow;
}
```
采用了ES2015的写法，下面是重点，我们在目录下新建一个`webpack.config.js`代码如下：
```javascript
module.exports = {
	entry: './main.js',
	output: {
		path: __dirname,
		filename: 'bundle.js'
	},
	module: {
		loaders: [
			{test: /\.css$/, loader: 'style!css'},
			{
				test: /\.js[x]?$/, 
				exclude: /node_modules/,
				loader: 'babel?presets[]=es2015&presets[]=react'
			}
		]
	}
}
```
这里主要看`loaders`，`loaders`是一个数组，里面每一个对象对应一种加载器，这里我们分别设置了css的加载器以及ES2015和React的解析器。

最后我们通过`webpack-dev-server`来启动工程，请执行下面的命令：
```bash
webpack-dev-server --progress --color
```
`--progress --color`参数可以让我们方便看到webpack在编译过程中的进度信息。然后访问
[http://localhost:8080/webpack-dev-server/index.html](http://localhost:8080/webpack-dev-server/index.html)然后会看到：
![img](img/hw.jpg)

`webpack-dev-server`自带了`livereload`的功能，当你改变了依赖的js、css会重新编译变化的部分，然后自动刷新浏览器，大大提高了开发效率，比如我们把背景改为绿色，就会看到下面:
![img](img/hw1.jpg)

### 其他
我创建了这样一个种子项目，[react-webpack-es6-seed](https://github.com/ShadowZheng/react-webpack-es6-seed)，欢迎Star。

你可以直接用它作为一个基础来构建你的项目。（完）

### 推荐

如果学习webpack，强烈推荐[react-webpack-cookbook](http://christianalfoni.github.io/react-webpack-cookbook/)这本电子书。你将会受益良多。（完）
