# co的嵌套使用

上一节我们演示了 yield thunk 用法，解答了为什么异步函数必须包装成 thunk 偏函数的形式。

yield 难道只能是 thunk ？显然不是，理论上 yield 后面可以跟上所有的数据类型，只是在现实场景中最具有意义的是 thunk，还有 generator function。

这一节我们将探讨如果 yield 后面是 generator function 的情况。

我们需要判断 yield 后面跟的是 generator function 还是 普通 function：

    //判断是否是 generator function
    function isGeneratorFunction(obj) {
        var constructor = obj.constructor;
        if (!constructor) return false;
        if ('GeneratorFunction' === constructor.name || 'GeneratorFunction' === constructor.displayName) return true;
        return isGenerator(constructor.prototype);
    }
    //判断是否是 generator 对象
    function isGenerator(obj) {
        return 'function' == typeof obj.next && 'function' == typeof obj.throw;
    }
    
generator function 的特征是 constructor.name === 'GeneratorFunction'。

当初始化 generator function 后返回的是 generator 对象，特征是带有 next 与 throw 方法。

改造下上一节的 co 代码：

    function co(fn) {
        return function(done) {
            var ctx = this;
            var gen = fn.call(ctx);
            var it = null;
            function _next(err, res) {
                if(err) res = err;
                it = gen.next(res);
                //{value:function(){},done:false}
                if(!it.done){
                    if(isGeneratorFunction(it.value)){
                        co(it.value).call(ctx,_next);
                    }else{
                        it.value(_next);
                    }
                }else{
                    done && done.call(ctx);
                }
            }
            _next();
        }
    }
    
代码会比上一节来得复杂，关键看：

    if(!it.done){
        if(isGeneratorFunction(it.value)){
            co(it.value).call(ctx,_next);
        }else{
            it.value(_next);
        }
    }else{
        done && done.call(ctx);
    }
    
isGeneratorFunction(it.value) 我们判断 yield 后是否是 generator function ，如果是递归调用 co() 方法，递归执行该 generator function 的逻辑，直到 done : true，调用外层的 _next() 方法(done && done.call(ctx);)，执行下一个yield。

使用 demo ：

    function read(file) {
        return function(fn){
            fs.readFile(file, 'utf8', fn);
        }
    }
    
    function *gf1(){
        this.a = yield read('error.js');
    }
    
    function *gf2(){
        this.b = yield read('package.json');
    }
    
    co(function *(){
        yield gf1;
        yield gf2;
        console.log(this.a.length);
        console.log(this.b.length);
    })();
    
有没有点 koa 中间件的赶脚？已经是雏形了哦，下一节我们将利用 co 实现 koa 的中间件机制。

yield gf1 ，gf1 是个 generator function ，co 包裹的遍历器执行到这里的时候，进入：

    if(isGeneratorFunction(gf1)){
        co(gf1).call(ctx,_next);
    }
    
递归调用 co () ，执行 gf1 遍历器中的逻辑，直到 gf1 的 done=true：

    if(!it.done){
        //...
    }else{
        _next && done._next(ctx);
    }

调用上一层的 _next() 执行下一个 yield，demo中是 yield gf2 ，循环执行直到done=true。


