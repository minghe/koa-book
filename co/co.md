# co 的实现

[co](https://github.com/tj/co/blob/master/index.js) 的实现非常精巧，严重推荐阅读 co 的源码。

co 代码优化过，新的4.x.x的代码基于Promise重构过，比以前的代码好理解很多。

上一个版本的可以看[3.1.0](https://github.com/tj/co/blob/3.1.0/index.js)的代码，很晦涩，不好理解。

4.x.x 的 api 有些变化，co 返回的是一个 promise，这部分的内容留到最后一节讲解。

一个最简单的 co 实现：

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
                    it.value(_next);
                }
            }
            _next();
        }
    }

调用：

    var fs = require('fs');
    //一个 thunk 函数
    function read(file) {
        return function(fn){
            fs.readFile(file, 'utf8', fn);
        }
    }
    co(function *(){
        var c = 2;
        console.log(c);
        var a = yield read('error.js');
        console.log(a.length);
    
        var b = yield read('package.json');
        console.log(b.length);
    })();
    
请自行复制以上代码，并给觉得有疑惑的地方打上断点，查看逻辑的执行流程。
    
我们需要解开如下几个关键问题：

* 问题1：为什么异步函数需要封装成 thunk 偏函数的形式？
* 问题2：var a = yield read('error.js'); 为什么 a 的值是 read() 异步返回的数据？
* 问题3：co 接受的 generator function 内部执行逻辑与co内部逻辑的执行顺序是什么样的？

（PS:  yield 后的内容称之为 yieldable）

var gen = fn.call(ctx); **fn** 是co的实参，为一个generator function，再次强调 generator function 调用时，只定义不执行，与普通函数不同。

co 的核心是想办法递归调用 generator function 的 next()，直到执行完毕，_next() 就是个这样的递归方法：

    function _next(err, res) {
        if(err) res = err;
        it = gen.next(res);
        //{value:function(){},done:false}
        if(!it.done){
            it.value(_next);
        }
    }
    
    _next();
    
第一次调用 _next() ，执行到 gen.next(res); 开始执行  generator function 遍历器的逻辑：

    var c = 2;
    console.log(c);
    yield read('error.js');
    
留意不是执行到 **var a =** 给a赋值（赋值是在 yield 逻辑之后）的逻辑，遍历器执行到 yield 关键字就中断了，开始执行 read 这个 thunk 的逻辑。

read是一个偏函数，只做一件事情，返回一个函数：

    function(fn){
        fs.readFile(file, 'utf8', fn);
    }
    
核心问题：这里的 fn 的实参是什么？  

执行完 read() 后，执行 **it =** （co中的 it = gen.next(res); ）的赋值，it的值是：

    {value:function(fn){
       fs.readFile(file, 'utf8', fn);
    },done:false}
    
（参见第一节 generator）value 即偏函数 return 的函数，因为遍历器知道后面还有 yield 标识，也就值没有执行完，所以 done 为 false。

接下来我们执行 value 函数：

    if(!it.done){
        it.value(_next);
    }

解答前面的核心问题，function(fn){fs.readFile(file, 'utf8', fn);} **fn** 指的是 _next 遍历方法。

正式执行异步逻辑获取文件数据：fs.readFile(file, 'utf8', fn); 且执行完后，执行 _next()。

所以 _next 带有二个形参：**err**, **res**，这是 fs.readFile(err,res) 的回调函数的形参。

res 就是读取文件后返回的文件内容。

这就解答了前面的 **第一个问题**，为什么异步函数需要封装成 thunk 偏函数的形式，因为我们需要在异步执行完成后，触发 _next 遍历方法。

_next 进入第二次调用，又执行了次 gen.next(res) 将 res 塞回到遍历器内部，且遍历器执行第二段逻辑：

    var a = res;
    console.log(a.length);
    yield read('package.json');
    
这就解答了前面的 **第二个问题**，var a = yield read('error.js'); 为什么 a 的值是 read() 异步返回的数据？那是因为 a 的值是 gen.next() 塞回去的。

执行到 yield 关键字时，又中断了，执行 read 偏函数，it.value(_next) 执行偏函数的返回函数，异步读取文件内容。

读取完文件后，又再次触发了 _next() 方法 直到 done 为 true 时。

这就解答了 **第三个问题**：co 接受的 generator function 内部执行逻辑与co内部逻辑的执行顺序是什么样的？

当你搞清楚了这三个问题，就清楚了 co 的实现原理。

