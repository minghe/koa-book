# co 的简单使用

    var co = require('co');
    var fs = require('fs');
    
    function read(file) {
      return function(fn){
        fs.readFile(file, 'utf8', fn);
      }
    }
    co(function *(){
    
      var a = yield read('.gitignore');
      console.log(a.length);
      
      var b = yield read('package.json');
      console.log(b.length);
    });

co 要求所有的异步函数必须是偏函数，称之为 **thunk** :

    function read(file) {
      return function(fn){
        fs.readFile(file, 'utf8', fn);
      }
    }
    
上述代码封装了 fs.readFile() 异步读取文件的方法。

node 异步方法的回调函数规范是 function(err,result){} ,co 会处理该形式的函数，result或error会在异步函数执行完毕后获取到。

如果需要对 thunk 的返回数据做下处理，可以这么写：

    function read(file) {
      return function(fn){
        fs.readFile(file, 'utf8', function(err,result){
            if (err) return fn(err);
            fn(null, result);
        });
      }
    }

如果嫌写thunk函数别扭，可以使用 [thunkify](https://www.npmjs.com/package/thunkify)：

    var thunkify = require('thunkify');
    var fs = require('fs');
     
    var read = thunkify(fs.readFile);

获取 thunk 函数的结果，只要使用 yield 关键字：

    var a = yield read('.gitignore');
    console.log(a.length);

可以看下在 co 中，我们无需使用控制流程的 next 方法，co 已经封装了 generator function 的流转。
