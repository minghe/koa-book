# xtemplate的使用

[xtemplate](https://www.npmjs.com/package/xtemplate) 模板引擎在前后端都可以使用，nodejs端使用需要使用 [xtpl](https://www.npmjs.com/package/xtpl) 模块。

package.json定义依赖：

    "xtemplate": "4.2.4",
    "xtpl": "3.1.9"
    
### 在 koa 中使用

    //xtemplate模板引擎对koa的适配
    var xtpl = require('xtpl/lib/koa');
    //xtemplate模板渲染
    xtpl(app,{
        //配置模板目录，指向工程的view目录
        views: config.viewDir
    });
    
在 view 目录下放一个简单的 test.xtpl 模板（xtemplate 模板统一使用.xtpl后缀），内容如下：

    <h2>{{title}}</h2>

写一个简单的 router：

    app.get('/view-test',function *(){
        yield this.render('test',{"title":"xtemplate demo"});
    })

特别注意，this.render() 必须使用 yield，刚开始写会经常忘记掉，需要多留心。
    
render() 第一个参数为模板路径（无需加.xtpl后缀），比如模板路径为 view/test/a.xtpl ，参数值为 test/a ；第二个参数为传递给引擎处理的数据，demo中 {{title}} 占位变量 会被字段 title 所替换。

### xtpl 适配 koa 的原理

理解 xtpl 如何让 xtemplate 适配 koa 有助于理解如何给 koa 实例上下文挂载方法。

app.context 是非常重要的属性，为 koa实例的上下文对象，中间件或插件模块可以复写或添加 context 的方法与属性值。

xtpl 就是复写了 context.render 方法，用于渲染 xtemplate 模板：

    function *render(path, data, opt) {
        //xtplRender 为xtemplate引擎渲染方法
        var html = yield xtplRender(Path.resolve(views, path + '.xtpl'), data);
        if (!opt || opt.write !== false) {
            this.type = 'html';
            this.body = html;
        }
        return html;
    }
    
    app.context.render = render;
    
xtemplate 渲染完成后，将 html 字符串覆盖 this.body 上下文属性，当所有中间件都运行结束后，koa 就会把 body 的内容吐到页面上。

理解了 xtpl 包装原理，我们就可以实现自己的包装器，比如所有的页面都会打印用户名称：

    app.context.html = function *(viewName,data){
        if(data){
            data.nick = '明河';
        }
        yield this.render(viewName, data);
    }

路由中使用：

    app.get('/view-test',function *(){
        yield this.html('test',{"title":"xtemplate demo"});
    })
    
这样就不需要每次渲染模板时，都要传递 nick 字段。

