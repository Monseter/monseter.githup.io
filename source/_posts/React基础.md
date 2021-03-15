---
title: React 环境搭建
typora-root-url: ./
date: 2021-03-01 10:39:33
tags: 前端框架
categories: React
---

## 安装

### 1、安装全局包

```
npm install babel -g
npm install webpack -g
npm install webpack-dev-server -g
```

### 创建根目录

创建一个根目录，目录名为：reactApp，再使用 npm init 初始化，生成 package.json 文件：

```
About to write to D:\MyProject\react-demo\package.json:

{
  "name": "react-demo",
  "version": "1.0.0",
  "description": "学习react",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "https://gitee.com/qingitee/react-demo.git"
  },
  "author": "",
  "license": "ISC"
}


Is this OK? (yes)
```

一路回车到底，生成 `package.json` 文件即可

### 2、添加依赖包及插件

因为我们要使用 React, 所以我们需要先安装它，--save 命令用于将包添加至 package.json 文件。

```javascript
// 安装 react
npm install react --save

// 安装 dom 相关操作功能
npm install react-dom --save

// 同时也要安装一些 babel 插件 以将 ES6 代码转为 ES5 代码，这样我们就能在目前不支持 ES6 浏览器上执行 React 代码。Babel 内嵌了对 JSX 的支持。通过将 Babel 和 babel-sublime 包（package）一同使用可以让源码的语法渲染上升到一个全新的水平
npm install babel-core
npm install babel-loader
npm install babel-preset-react
npm install babel-preset-es2015
```

### 3、创建文件

接下来我们创建一些必要文件：

```
$ touch index.html
$ touch App.jsx
$ touch main.js
$ touch webpack.config.js
```

### 4、配置 `webpack.config.js`

```javascript
var config = {
    // 指定打包的入口文件
    entry: './main.js', 
    
    // 配置打包结果，path定义了输出的文件夹，filename则定义了打包结果文件的名称。
    ouput: { 
        path: './',
        filename: 'index.js'
    },
    
    // 设置服务器端口号为 7777，端口后你可以自己设定 。
    devServer: {
      	inline: true,
      	port: 7777
   	},
   
    // 定义了对模块的处理逻辑，这里可以用loaders定义了一系列的加载器，以及一些正则。当需要加载的文件匹配test的正则时，就会调用后面的loader对文件进行处理，这正是webpack强大的原因。
    module: {
      loaders: [ {
         test: /\.jsx?$/,
         exclude: /node_modules/,
         loader: 'babel',
			
         query: {
            presets: ['es2015', 'react']
         }
      }]
   }
    
}
```

现在打开 **package.json** 文件，找到 **"scripts"** 中的 **"test" "echo \"Error: no test specified\" && exit 1"** 使用以下代码替换：

```
"start": "webpack-dev-server --hot"
```

替换后的 **package.json** 文件 内容如下：

```
{
  "name": "react-demo",
  "version": "1.0.0",
  "description": "学习react",
  "main": "index.js",
  "scripts": {
    "start": "webpack-dev-server --hot"  // 配置热更新
  },
  "repository": {
    "type": "git",
    "url": "https://gitee.com/qingitee/react-demo.git"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "babel-core": "^6.26.3",
    "babel-loader": "^8.2.2",
    "babel-preset-es2015": "^6.24.1",
    "babel-preset-react": "^6.24.1",
    "react": "^17.0.1",
    "react-dom": "^17.0.1"
  }
}

```

现在我们可以使用 **npm start** 命令来启动服务。**--hot** 命令会在文件变化后重新载入，这样我们就不需要在代码修改后重新刷新浏览器就能看到变化。

### 5、index.html

设置 **div id = "app"** 为我们应用的根元素，并引入 **index.js** 脚本文件。

```html
<!DOCTYPE html>
<html>

    <head>
        <meta charset = "UTF-8">
        <title>React App demo </title>
    </head>

    <body>
        <div id = "app"></div>
        <script src = "index.js"></script>
    </body>

</html>
```

### 6、App.jsx 和 main.js

这是第一个 react 组件。这个组件将输出 **Hello World!!!**。

#### 7、App.jsx 文件代码

```javascript
import React from 'react';

class App extends React.Component {
    render() {
        return {
            <div> Hello World!!!!</div>
        }
    }
}
```

我们需要引入组件并将其渲染到根元素 **App** 上，这样我们才可以在浏览器上看到它。

### 8、main.js 文件代码

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';

ReactDOM.render(<App />, document.getElementById('app'))
```

**注意：**

如果想要组件可以在任何的应用中使用，需要在创建后使用 **export** 将其导出，在使用组件的文件使用 **import** 将其导入。

### 9、运行服务

完成以上配置后，我们即可运行该服务：

```
npm start
```

## React 脚手架安装

### 1、安装脚手架

```
npm install -g create-react-app
```

### 2、创建项目

```
create-react-app my-app
```

## 3、启动项目

```
npm start
```

## 如何在 react 中使用 less

目前创建react项目一般使用 create-react-app 这个脚手架工具来创建，但这种方式默认没有对于less的配置。所以我们自己配置一下：

### 1、暴露出 webpack 配置文件

使用create-react-app 创建的项目默认是看不到webpack配置文件的，需要先暴露出来，然后才能修改里面的配置信息。

终端运行一下命令（**注意：此命令一旦运行会修改package.json 文件，不可回退**）

```
npm run eject
```

### 2、安装less和less-loader

运行以下命令：

> npm install less-loader less --save

如果遇到安装失败的情况，请改用cnpm。

### 3、修改webpack配置文件

运行完以上命令后，项目根目录下会多出一个 **config** 文件夹，找到里面的 **webpack.config.js** 这个文件

添加以下代码：

```javascript
const lessRegex = /\.less$/;
const lessModuleRegex = /\.module\.less$/;
```

`oneof 处`配置以下代码

```
{
  test: lessRegex,
  exclude: lessModuleRegex,
  use: getStyleLoaders(
    {
      importLoaders: 2,
      sourceMap: isEnvProduction && shouldUseSourceMap,
    },
    'less-loader'
  ),
  // Don't consider CSS imports dead code even if the
  // containing package claims to have no side effects.
  // Remove this when webpack adds a warning or an error for this.
  // See https://github.com/webpack/webpack/issues/6571
  sideEffects: true,
},
// Adds support for CSS Modules, but using SASS
// using the extension .module.scss or .module.sass
{
  test: lessModuleRegex,
  use: getStyleLoaders(
    {
      importLoaders: 2,
      sourceMap: isEnvProduction && shouldUseSourceMap,
      modules: true,
      getLocalIdent: getCSSModuleLocalIdent,
    },
    'less-loader'
  ),
},
```

这样就配置完成，可以直接使用less了。

# TypeError: this.getOptions is not a function

![image-20210302135958589](image-20210302135958589.png)

**原因：** less-loader安装的版本过高
**解决方案：** 1.npm uninstall less-loader
2.npm install less-loader@5.0.0