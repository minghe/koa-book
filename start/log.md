# 日志记录

日志是应用不可缺少的部分，但 koa 没有日志记录中间件，我们使用 [mini-logger](https://www.npmjs.com/package/mini-logger) 。

mini-logger 会创建日志记录文件，自动使用日期归档，而且还可以自定义日志分类。

    var Logger = require('mini-logger');
    var logger = Logger({
        dir: config.logDir,
        categories: [ 'router','model','controller'],
        format: 'YYYY-MM-DD-[{category}][.log]'
    });
    
* dir ：指定日志放在哪里
* categories ：自定义日志分类
* format ：日志文件名格式

记录 Error() 实例：

    logger.error(new Error('test'));
    
使用 error() ，会将出错堆栈信息输出到 xxxx-xx-xx-error.log 文件中。

当定义自定义分类后，会自动注册分类日志方法，比如定义了 categories: ['router'] ：

    logger.router('test router');
    
就有了 logger.router() 方法，并将日志记录到 xxxx-xx-xx-router.log 文件中。

给日志分类，让我们排查线上问题更有效率。





