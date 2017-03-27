# koa路由

koa是个极简的web框架，简单到连路由模块都没有配备，多个页面在koa中要这么写：

    app.use(function *(){
        //我是首页
        if(this.path==='/'){
        
        }
    });
    
    app.use(function *(){
        //我是详情页
        if(this.path==='/detail'){
        
        }
    });
    
这么挫的写法，显然无法支撑真正的业务应用（工具、功能模块这么写是没问题的）。

我们需要引入个路由中间件 [koa-router](https://www.npmjs.com/package/koa-router) ，有了 koa-router 我们就可以像写 express 路由一样，写 koa 应用页面：

注入 koa-router 中间件：

    var router = require('koa-router')();
    app.use(router.routes());

简单的路由写法：

    router.get('/', function *(next) {
        //我是首页
        //this 指向请求
    });
    router.get('/detail/:id', function *(next) {
        //我是详情页面
        //:id 是路由通配规则，示例请求 /detail/123 就会进入该 generator function 逻辑
        var id = this.params.id; //123
    });
    
除了最常用的get()外，还有 post() 、put() 、patch() 、delete()，后面会结合具体业务场景讲解。

koa-router 拥有丰富的 api 细节，用好这些 api ，可以让页面代码更为优雅与可维护。

比如 param() 方法，用于路由参数的处理：

    router.param('id',function *(id,next){
        this.id = Number(id);
        if ( typeof this.id != 'number') return this.status = 404;
        yield next;
    }).get('/detail/:id', function *(next) {
        //我是详情页面
        var id = this.id; //123
        this.body = id;
    });
    
param() 用于封装参数处理中间件，当访问 /detail/:id 路由时，会先执行 param() 定义的 generator function 逻辑。

函数的第一个是路由参数的值，next 是中间件流程关键标识变量。

    yield next;
    
表示执行下一个中间件。

通过 param() ，我们可以把参数的处理给抽象出来。



