# mocha异步测试

在 nodejs 中有非常多的异步逻辑，这些异步逻辑该如何测试呢？

    var fs = require('fs');
    var should = require('should');
    describe('fs', function() {
        describe('#readFile()', function() {
            it('should not be null', function(done) {
                fs.readFile('./package.json', 'utf8', function(err,res){
                    if (err) throw err;
                    res.should.not.equal(null);
                    done();
                });
            });
        });
    });
    
关键在于 done 实参，必须在执行完异步后（在异步回调中）执行下 done()，就能捕获到用例。

回调函数 done() 支持接收一个错误：done(err)，用于简化错误处理。

