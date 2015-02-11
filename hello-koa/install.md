# 安装与运行

### 安装 koa

由于使用了JavaScript 1.7得新特性，所以Koa要求Node版本至少在 **0.11.x** 以上，且开启 harmony。 

mac下推荐安装 [n](https://www.npmjs.org/package/n) 模块，非常方便地安装多个 node 版本，可以切换使用：

    sudo npm install -g n
    sudo n stable
    
（ps：stable 使用最新稳定的Node版本）

win下可以在nodejs的[下载列表](http://nodejs.org/dist/)中找到。

### 安装 generator-k

[generator-k](https://www.npmjs.com/package/generator-k) 是我写的 koa 项目工程生成器，带有经过筛选的优秀中间件，生成即用。

    npm install -g yo generator-k

generator-k 依赖于 [yoeman](http://yeoman.io/)，所以必须安装 yo。

安装成功后，创建一个 book 目录（作为demo工程），并进入，然后运行：

    yo k
    
k 会让你选择是否使用数据库，可以选择 none ，暂时跳过。

生成的目录结构如下：

    .
    ├── controller
    │   └── index.js 
    ├── router
    │   └── index.js
    ├── model
    ├── config
    |   ├── config
    |   └── local 
    ├── public
    |   ├── js
    |   └── css 
    ├── test
    |   └── index-router-spec.js
    ├── view
    |   ├── index.xtpl
    |   └── layout
    ├── app.js
    └── package.json

后面会详细讲解 koa 的代码组织，这一节我们先跳过。

### 运行应用

我们在 book 目录下运行：

    node --harmony app.js

必须加 --harmony ，这样才会支持 ES6 语法。
    
如果看到 “listening on port 3000”，说明启动成功了，可以访问 [http://localhost:3000]试试。

为了日后运行方便，可以设置个别名：

    alias nod="node --harmony"
    
这样日后运行，依旧只要使用：

    node app.js
    
app.js 的核心代码是：

    var koa = require('koa');
    var app = koa();
    app.listen(3000);




