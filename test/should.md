# should断言库

node 内置的断言库 assert ，功能比较弱，不太好用，推荐使用 [should](https://www.npmjs.com/package/should) ，详细api可以看 [should.js](http://shouldjs.github.io/)。

should 的断言方法注入到 Object.prototype 中，所以断言的风格更符合用户思维习惯，也支持链式调用，跟 jQuery 有点像：

    var should = require("should");
    describe('Should test', function() {
        it('number', function() {
            (123).should.be.a.Number;
        });
        it('object property', function() {
            var obj = {name:'minghe',email:"minghe36@gmail.com"};
            obj.should.have.property('name','minghe');
            obj.should.have.property('email');
        });
    });

(123).should.be.a.Number 判断 123 是否是一个数字，适用于其他类型的判断。

obj.should.have.property('name','minghe') obj 对象是否包含属性 name ，且 name = 'minghe' 。

### 常用的 api

使用 ok 判断值是否为 true：

    it('ok',function(){
        (true).should.be.ok;
    })

使用 equal 判断一个值是否符合预期：

    it('equal',function(){
        'abc'.should.equal('abc');
    });
    
使用 not 取反：

    it('not equal',function(){
        'abc'.should.not.equal('ddd');
    });
    
判断值是否存在：
    
    it('exist',function(){
        var result = {};
        should.exist(result);
    })

更多的 api 请看 [文档](http://shouldjs.github.io/)。


    