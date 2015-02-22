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

    DEBUG=book NODE_ENV=local node --harmony app.js
    
多个环境变量，使用空格隔开。

访问页面后，输出的log是：

  book env: local +0ms
  
如果去掉环境变量 DEBUG=book 将不输出任何信息，这样当我们需要调试某个模块文件时，可以保证只有该模块的 log ，不会被其他模块所干扰。

如果我们想输出所有的调试信息呢？

可以使用 DEBUG=* 。




    

