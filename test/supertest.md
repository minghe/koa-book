# supertest请求测试

在 node 业务应用中，我们经常需要测试路由的可用性，如何处理呢？

可以使用 supertest 模块，supertest 专门用于 http 断言，支持 koa 的 http 请求测试。

为了保证应用的可测试性，我们需要把应用脚本比如 app.js 中的 koa 实例暴露出来：
 
    var koa = require('koa');
    var app = koa();
    //一堆中间件...
    module.exports = app;
    
用例写法：

    var superagent = require('supertest');
    var app = require('../app');
    
    function request() {
        return superagent(app.listen());
    }
    
    describe('Routes', function () {
        describe('GET /', function () {
            it('should return 200', function (done) {
                request()
                    .get('/')
                    .expect(200, done);
            });
        });
    });

superagent(app.listen()) 会截获 koa 的 http 请求，可以使用 get 、 post 等方法，对请求进行测试。

    request()
        .get('/')
        .expect(200, done);
        
get('/') 即测试首页 get 请求，.expect(200, done) 测试 请求状态码是否为 200 （请求成功），done 是必须传入的，这样请求测试结束后，才能把测试信息推送给mocha处理。

上述测试代码等价于：

    request()
        .get('/')
        .expect(200)
        .end(function(err, res){
            if (err) return done(err);
            done();
        });
        
.end() 回调会在请求完成后触发，可以在回调中对错误进行处理，res 包含完整的请求信息，可以对这些信息进行测试，比如页面输出的内容等。

运行命令 ：

    mocha --harmony
    
留意：测试 koa 的请求必须加--harmony，否者会抛异常。

我们经常需要对 json 接口的数据结构合法性进行测试，如何借助 supertest 实现测试呢？

我们新建个 /api/user/:id 的路由，返回一个用户信息：

    app.get('/api/user/:id',function *(){
        var user = {name:'minghe',email:'minghe36@gmail.com'};
        user = JSON.stringify(user);
        this.body = user;
    })
    
测试此路由是否返回正确的数据：

    it('should be json',function(done){
        request()
            .get('/api/user/1')
            .expect(200)
            .end(function(err, res){
                if (err) return done(err);
                var text = res.text;
                var json = JSON.parse(text);
                json.should.have.property('email');
                json.should.have.property('minghe');
                done();
            });
    })
    
supertest 很强大，可以设置请求的头信息，使用 set() ：

    request()
        .get('/')
        .set('Accept', 'application/json')
        .expect('Content-Type', /json/)    
        
而 expect() 除了支持状态码测试外，还支持头信息测试：.expect('Content-Type', /json/) 。

