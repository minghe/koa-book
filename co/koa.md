# koa 中间件机制实现原理

使用 koa 的同学一定会有如下疑问：

* koa 的中间件机制是如何实现？
* 为什么中间件必须是 generator function？
* next 实参指向是什么？为什么可以通过 yield next 可以执行下一个中间件？
* 为什么中间件从上到下执行完后，可以从下到上执行 yield next 后的逻辑？

接下来我们通过实现简单的 koa 框架（剥离除中间件外所有的逻辑）来解答上述问题，这个框架的名字叫 SimpleKoa：

    var co = require('co');
    
    function SimpleKoa(){
        this.middlewares = [];
    }
    SimpleKoa.prototype = {
        //注入个中间件
        use: function(gf){
            this.middlewares.push(gf);
        },
        //执行中间件
        listen: function(){
            this._run();
        },
        _run: function(){
            var ctx = this;
            var middlewares = ctx.middlewares;
            return co(function *(){
                var prev = null;
                var i = middlewares.length;
                //从最后一个中间件到第一个中间件的顺序开始遍历
                while (i--) {
                    //实际koa的ctx应该指向server的上下文，这里做了简化
                    //prev 将前面一个中间件传递给当前中间件
                    prev = middlewares[i].call(ctx, prev);
                }
                //执行第一个中间件
                yield prev;
            })();
        }
    };
    
写个 demo 印证下中间件执行顺序：

    var app = new SimpleKoa();
    
    app.use(function *(next){
        this.body = '1';
        yield next;
        this.body += '5';
        console.log(this.body);
    });
    app.use(function *(next){
        this.body += '2';
        yield next;
        this.body += '4';
    });
    app.use(function *(next){
        this.body += '3';
    });
    app.listen();
    
执行后控制台输出：123456，对照 koa 中间件执行顺序，完全一致！寥寥几行代码，我们就实现了 koa 的中间件机制！这就是 co 的魔力。
