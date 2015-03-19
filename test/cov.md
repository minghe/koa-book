# 测试覆盖率

我们写了测试用例，但如何知道用例的覆盖率呢？

可以使用 [istanbul](https://www.npmjs.com/package/istanbul) 实现单元测试覆盖率报告，istanbul 功能非常强大，支持与 mocha 的结合。

在应用工程中执行：

    npm install --save-dev istanbul
    

命令比较长，我们将其写入到 package.json 的 script 方便调用：

    "scripts": {
        "test": "NODE_ENV=local node --harmony node_modules/.bin/istanbul cover --report html ./node_modules/mocha/bin/_mocha -- 'test/**/*-spec.js'"
    }
    
* NODE_ENV=local node --harmony ：以本地环境、es6支持启动 node 应用；
* node_modules/.bin/istanbul cover --report html ：调用 istanbul 程序执行 cover 命令（执行覆盖率计算），--report html 生成的报告以 html 的形式；
* ./node_modules/mocha/bin/_mocha -- 'test/**/*-spec.js' ：关联 mocha 测试驱动程序，执行 tes 目录下 所有的后缀是 -spec.js 的用例文件。

运行 npm test ，如果一切正常，将会在工程目录中生成 coverage 目录，可以打开 coverage/index.html 查看覆盖率报告。



