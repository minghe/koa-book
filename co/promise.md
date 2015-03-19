# ES6的Promise

第三节说到 co 的4.x.x的代码通过 Promise 重构过，代码更为优雅和可读，co 是使用 Promise 的经典案例，所以借 co 来学习下 ES6 中的 Promise 对象。

（使用 koa 的好处是我们可以使用 ES6 新增的特性，所以学习 ES6 非常有必要，请看阮一峰老师的 [《ECMAScript 6 入门》]（http://es6.ruanyifeng.com/#docs/generator）。）

promise 是一种用于解决回调嵌套导致的“恶魔金字塔”问题的模式，ES6 中的 Promise 对象是对 promise 规范的经典实现。

ES6 中的 Promise API 与大家常用的 jQuery、[q](https://www.npmjs.com/package/q) 不大一样。

q 有个 defer() 用于定义 promise ：

    var deferred = Q.defer();
    FS.readFile("foo.txt", "utf-8", function (error, text) {
        if (error) {
            deferred.reject(new Error(error));
        } else {
            deferred.resolve(text);
        }
    });
    return deferred.promise;
    
ES6 中的 Promise API 与 [rsvp](https://www.npmjs.com/package/rsvp) 非常相似：

    var promise = new Promise(function(resolve, reject){
        FS.readFile("foo.txt", "utf-8", function (error, text) {
            if (error) {
                reject(new Error(error));
            } else {
                resolve(text);
            }
        });
    }
    return promise;
    
使用 promise （大家都比较熟悉了就不再累述）：

    promise.then(function(){
        
    }).fail(function(){
    
    })
    
### 使用 Promise 优化 co 代码

我们希望 yieldable 可以是 promise（将 read 函数由 thunk 转成 promise，其他代码不变）：

    function read(file) {
        return new Promise(function(resolve, reject){
            fs.readFile(file, 'utf8', function(err,result){
                if (err) reject(err);
                else resolve(result);
            });
        });
    }

我们来看下 co 是如何实现的。

首先，将所有的 yieldable 都转成标准 Promise 实例（简化演示，去掉 array 、object 转 promise 过程）：

    //将所有的 yieldable 都转成 promise 
    function toPromise(obj) {
      if (!obj) return obj;
      if (isPromise(obj)) return obj;
      //thunk 偏函数转成 promise
      if ('function' == typeof obj) return thunkToPromise.call(this, obj);
      //如果是 generator function 或 generator 对象 调用次 co 执行此遍历器逻辑
      if (isGeneratorFunction(obj) || isGenerator(obj)) return co.call(this, obj);
      return obj;
    }
    
    //判断是否是 promise
    function isPromise(obj) {
      return 'function' == typeof obj.then;
    }
    
    //thunk 偏函数转成 promise
    function thunkToPromise(fn) {
      var ctx = this;
      return new Promise(function (resolve, reject) {
        fn.call(ctx, function (err, res) {
          if (err) return reject(err);
          if (arguments.length > 2) res = slice.call(arguments, 1);
          resolve(res);
        });
      });
    } 

co返回 promise ，这样可以支持 多个 co 的链式调用：

    function co(gen) {
      var ctx = this;
      if (typeof gen === 'function') gen = gen.call(this);
    
      return new Promise(function(resolve, reject) {
        //执行遍历器逻辑
        onFulfilled();
        
        function onFulfilled(res) {
          var ret;
          try {
            //执行遍历器逻辑
            ret = gen.next(res);
            //ret : {value:function(){},done:false}
          } catch (e) {
            return reject(e);
          }
          next(ret);
        }
        
        function next(ret) {
          //done 为 true，执行完成
          if (ret.done) return resolve(ret.value);
          //将 yieldable 转成 Promise 实例
          var value = toPromise.call(ctx, ret.value);
          //yieldable 执行完成后 调用 onFulfilled() ，执行下一个 next()
          if (value && isPromise(value)) return value.then(onFulfilled, onRejected);
          return onRejected(new TypeError('You may only yield a function, promise, generator, array, or object, '
            + 'but the following object was passed: "' + String(ret.value) + '"'));
        }
        
        //出现 error 执行 reject()
        function onRejected(err) {
          var ret;
          try {
            ret = gen.throw(err);
          } catch (e) {
            return reject(e);
          }
          next(ret);
        }
        
      })
    }


### 参考

* [《ECMAScript 6 入门》](http://es6.ruanyifeng.com/#docs/generator）
* [《koa源码分析系列—co实现》](http://purplebamboo.github.io/2014/05/24/koa-source-analytics-2/)