# koa的错误处理

错误处理是应用健壮性非常重要的一部分，所以提前到这里来讲，koa 在错误处理的便利上比 express 好非常多。

koa 有 error 事件，当发生错误时，可以通过该事件，对错误进行统一的处理。

我们创建个 error.js：

    var koa = require('koa');
    var app = koa();
    
    app.on('error', function(err,ctx){
        if (process.env.NODE_ENV != 'test') {
            console.log(err.message);
            console.log(err);
        }
    });   
        
    app.listen(3000);
    
监听 error 事件：

* err 参数 ： 错误对象，留意抛异常时，请务必抛出 Error 对象，不能只是简单的字符串，这是良好的编码习惯
* ctx ：为发生请求的上下文对象

process.env.NODE_ENV 是环境变量配置，下一节我讲解 koa 应用的环境配置。

process.env.NODE_ENV != 'test' 即不是单元测试环境时。

我们注入个中间件，并抛出个异常：

    app.use(function *(){
        throw new Error('demo error');
    });
    
再次强调，抛出异常必须是 Error 实例。

运行：node --harmony error.js，打开 [http://localhost:3000](http://localhost:3000)，页面会打印出 “Internal Server Error” （这是koa对错误的默认处理）。

同时会触发 error 事件，打印出 err.message。

“Internal Server Error” 的页面信息过于简陋，我们引入 [koa-onerror](https://www.npmjs.com/package/koa-onerror) 中间件，优化错误信息。

koa-onerror 会在 process.env.NODE_ENV === 'development'（不存在环境变量时，也会输出错误信息） 时抛出错误堆栈信息。

    var onerror = require('koa-onerror');
    onerror(app);
    
再次运行试试，页面打印的信息就会变成：

    Error
    Looks like something broke!
    
            
    Error: demo error
        at Object.<anonymous> (/dev/node/k-demo/error.js:5:11)
        at GeneratorFunctionPrototype.next (native)
        at Object.respond (/dev/node/k-demo/node_modules/koa/lib/application.js:172:10)
        at GeneratorFunctionPrototype.next (native)
        at Object.<anonymous> (/dev/node/k-demo/node_modules/koa/node_modules/koa-compose/index.js:37:12)
        at GeneratorFunctionPrototype.next (native)
        at next (/dev/node/k-demo/node_modules/koa/node_modules/co/index.js:83:21)
        at Object.<anonymous> (/dev/node/k-demo/node_modules/koa/node_modules/co/index.js:56:5)
        at Server.<anonymous> (/dev/node/k-demo/node_modules/koa/lib/application.js:123:8)
        at Server.EventEmitter.emit (events.js:110:17)
            
比 “Internal Server Error” 提供的信息多的多。

