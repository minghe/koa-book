# koa是什么

[koa.js](http://koajs.com) 是下一代的node.js框架，由Express团队开发，通过生成器（generators JavaScript 1.7新引入的，用于解决回调嵌套的方案），减少异步回调，提高代码的可读性和可维护性，同时改进了错误处理（Express的错误处理方式相当糟糕）。

koa 非常小，只有550的代码(ps：这个数字不准，只要知道Koa很精巧即可)，koa 内核中没有捆绑任何中间件，连常用的 post body 解析都没有...

但不用担心，koa 拥有极其强悍的拓展性，在 koa 体系内书写中间件是一种享受。


### koa 相比 express 的优点在哪

koa 的先天优势在于 generator，带来的主要好处如下：

* 更优雅、简单、安全的中间件机制，后面章节会详细说明
* 更优雅、简单的异常处理
* 更优雅、简单的异步编程方式

koa 与 express 是共享底层库的，如果你会使用 express ，那么只要理解 generator 与 koa 框架 api，就可以快速上手。

如果你没有使用 express 的经验，那么我建议你直接使用 koa ，学习的曲线是先难后易，先苦后点，最后是满满的感动...

网上有部分同学担心 koa 的稳定性问题，阿里已经基于 koa 做业务开发了，没有出现过重大故障，如果基于koa的应用经常挂，只能是自己编写的业务代码不够健壮导致的，所以大可不必担心。

koa 生态圈处于高速发展地阶段，建议使用第三方模块时，依赖指定具体版本，不要选择使用最新版本：

    "dependencies": {
        "<name>": "<version>"
    }

### 跟 koa 相关的几位大神

* [TJ](https://github.com/tj) 无需多介绍，大量流行 node 模块作者
* [不四](https://github.com/dead-horse) 不四是阿里的同学，包装了大量的 koa 中间件，也是 koa 主要的维护者
* [苏千](https://github.com/fengmk2) 天朝 node 专家



### 参考

* [koa中文文档](http://koa.rednode.cn/)
* [Introduction to Generators & Koa.js: Part 1](http://code.tutsplus.com/tutorials/introduction-to-generators-koajs-part-1--cms-21615)
* [Introduction to Generators & Koa.js: Part 2](http://code.tutsplus.com/tutorials/introduction-to-generators-koajs-part-2--cms-21756)



