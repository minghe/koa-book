# 应用环境配置

我们通常通过环境变量，来区分各个应用的部署环境，根据不同的环境使用不同的配置，比如在开发环境中，我们将错误堆栈信息直接抛到页面中，而在生产环境中，我们会跳转到一个友好的错误页面。

启动应用时，可以带上定义的环境变量：

    NODE_ENV=development node --harmony app.js
    
我们配置了个 NODE_ENV 环境变量，定义为开发环境（development）。

（PS: WIN下配置环境变量的方式不同，需要使用 set NODE_ENV=development。）
 
打开 config/config.js，可以找到针对开发环境做的配置：

    var _ = require('underscore');
    //本地/开发环境配置
    var local = require('./local');
    if(process.env.NODE_ENV === 'local' || process.env.NODE_ENV === 'development'){
        //使用local.js中的配置覆盖config.js中的配置
        config = _.extend(config,local);
    }

process.env 包含了应用所有配置的环境变量。
    
local.js 的配置内容如下：

    module.exports = {
        "env":"local",
        "debug": true
    };
    
为了方便中间件获取应用配置，我们将配置信息，注入到中间件上下文：

    var config = require('./config/config');
    app.use(function *(next){
        //config 注入中间件，方便调用配置信息
        if(!this.config){
            this.config = config;
        }
        yield next;
    });
    
路由中获取配置：
    
    app.get('/config',function *(){
        var config = this.config;
        this.body = config.env;
    });
    
页面输出是 local ，说明生产环境中 env 配置项已经被 local.js 中的配置所覆盖。


可以在 webstorm 中配置环境变量，第一章有提到：

![config](./hello-koa/images/editor-3.png)

![config](../hello-koa/images/editor-4.png)