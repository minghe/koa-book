# debug模块的使用

通常我们使用 console.log() 来打印 log 信息，log 一多对排查问题反而形成干扰，同时有些log我们希望只存在于开发环境。

所以，推荐使用 debug 模块，debug 模块可以对log进行分组，而且只有设置了环境变量 DEBUG 的情况下才会输出 log 。

    var debug = require('debug')('book');
    app.get('/config',function *(){
        var config = this.config;
        debug('env: %s',config.env);
        this.body = config.env;
    });
    
配置环境变量：DEBUG=book ，这样只会输出 book 分组的 log 信息。


    

