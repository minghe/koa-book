# node单元测试初见

### 为什么需要单元测试

前面明河使用二章的内容介绍了 koa 的基础使用与环境配置，这一章我们将用几节时间去讲解 node 的单元测试。

每一本 node 书籍都会强调单元测试的重要性，但都把单元测试的章节放在靠后的位置，不利于引起读者的重视。

单元测试的重要就像城市离不开下水道，虽然一般用户感知不到，但一旦没有或不健全，就是灾难，参见天朝的城市...

浏览器端 js 的单元测试，因为前端业务的多变性和对dom的依赖，业务代码的单测一直很难展开，而 node 应用不存在这个问题，node 中没有dom，而且变化会比前端少，稳定性诉求更高。

node 中单元测试容易展开，且成效好。

所以很有必要且应该在第三章讲解单元测试部分的内容，后面章节 demo 的演示，明河会尽量使用单测用例来演示。

### node 中的单元测试

[mocha](http://mochacn.github.io/) 是最好的 node 单元测试框架，使用简单且灵活，是进行 node 单测的首选。
 
webstorm 已经集成 mocha 。

安装 mocha：

    npm install -g mocha    

在 test 目录我们新建一个 demo-spec.js（约定-spec为用例文件） 文件，测试数组的 indexOf() 方法：

    var assert = require("assert");
    describe('Array', function() {
        describe('#indexOf()', function() {
            it('should return -1 when the value is not present', function() {
                assert.equal(-1, [1,2,3].indexOf(5));
                assert.equal(-1, [1,2,3].indexOf(0));
            });
        });
    });
    
assert 模块是 node 内置的断言库。

describe() 用于定义测试用例组，是可以嵌套的。

it() 是定义具体的测试用例。

assert.equal() 用于判断值是否符合预期。

**命令行运行用例** ：

    mocha

默认会运行 test 目录下的所有用例文件，输出的信息：

    Array
    #indexOf()
      ✓ should return -1 when the value is not present

也可以使用 webstorm 来运行 mocha。

点击菜单 “Run”，选择配置编辑，添加个 mocha :

![http://gtms03.alicdn.com/tps/i3/TB1L97yHpXXXXaQaXXX0qnIJpXX-1460-708.png](http://gtms03.alicdn.com/tps/i3/TB1L97yHpXXXXaQaXXX0qnIJpXX-1460-708.png)

同样可以配置 node 参数，比如增加 --harmony ,点击运行按钮后：

![http://gtms02.alicdn.com/tps/i2/TB1Uc3EHpXXXXb4XpXXpWgeTFXX-594-161.png](http://gtms02.alicdn.com/tps/i2/TB1Uc3EHpXXXXb4XpXXpWgeTFXX-594-161.png)

webstorm 只会显示出错的用例。

除了使用命令行与 webstorm 外还可以借助 npm 运行 测试脚本，在 package.json 中配置：

    "scripts": {
        "test": "mocha --harmony"
    }
    
运行命令 npm test ，就会执行 mocha --harmony 。



