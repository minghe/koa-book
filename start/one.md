# koa应用初见

打开 [generator-k](https://www.npmjs.com/package/generator-k) 生成的app.js 。

代码有些多，不利于作为上手学习，我们创建个 book/demo.js，内容如下：

    var koa = require('koa');
    var app = koa();
    
    app.use(function *(){
        var path = this.path;
        this.body = path;
    });
    
    app.listen(3000);
    
运行命令：

    node --harmony demo.js
    
访问 [http://localhost:3000](http://localhost:3000)，页面会打印出当前 url 的路径 “/”，使用 [http://localhost:3000/demo](http://localhost:3000/demo) 访问试试，输出的是/demo。

我们来看下代码含义。

初始化 koa ：

    var koa = require('koa');
    var app = koa();
    
使用 app.use() 注入中间件，这是与 express 最大不同的地方。

所有的koa中间件，必须是 generator function ，即 function *(){} 语法，不然会报错。

中间件 的上下文 this，指向用户当前的请求，中间件只有在请求时才会触发逻辑，比如获取当前请求的路径，可以使用：

    app.use(function *(){
        var path = this.path;
    });
    
建议在中间件中打个断点，看一下 this 的信息，非常丰富，后面会展开论述。

this.body 用于控制输出到页面的内容，比如：

    app.use(function *(){
        this.body = '<p>我是个html片段</p>';
    });
    
可以注入多个中间件，koa 会从上到下执行，然后从下到上执行，关于中间件的执行顺序问题，后面会展开论述。

    app.use(function *(){
        this.demo = 'test text';
    });
    
    app.use(function *(){
        this.body = this.demo;
    });

app.use() 究竟发生了什么不可思议的化学反应呢？

其实 app.use() 就干了一件事，就是将中间件放入一个数组，真正执行逻辑的是：

    app.listen(3000);
    
koa 的 listen() 除了指定了 http 服务的端口号外，还会启动 http server，等价于：

    var http = require('http');
    http.createServer(app.callback()).listen(3000);
    
后面这种繁琐的形式有什么用呢？

一个典型的场景是启动 https 服务，默认 app.listen(); 是启动 http 服务，启动 https 服务就需要：

    var https = require('https');
    https.createServer(app.callback()).listen(3000);
    
关于 https 服务后面章节会展开论述，这里先跳过。







    



