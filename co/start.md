# ES6的generator

ES6的generator 是 koa 的基础，想要用好 koa 离不开对 generator 的理解。

定义一个 generator 函数：

    var generator_func = function* () { };
    
接下来我们要用到 **yield** 关键字，用于停止执行和保存当前的堆栈。

我们通过一个数字的例子来演示其用法：


    var r = 3;
    
    function* infinite_ap(a) {
        for( var i = 0; i < 3 ; i++) {
            a = a + r ;
            yield a;
        }
    }
    
    var sum = infinite_ap(5);
    
    console.log(sum.next()); // returns { value : 8, done : false }
    console.log(sum.next()); // returns { value : 11, done: false }
    console.log(sum.next()); // returns { value : 14, done: false }
    console.log(sum.next()); //return { value: undefined, done: true }
    

infinite_ap generator定义了一个执行3次的循环，每次执行，给a变量加3。

yield a 会暂停执行并保存当前堆栈，返回当前的a。

generator 函数与普通函数不同，只定义遍历器，而不会执行，每次调用这个遍历器的next方法，就从函数体的头部或者上一次停下来的地方开始执行，直到遇到下一个yield语句为止。

yield语句就是暂停标志，next方法遇到yield，就会暂停执行后面的操作，并将紧跟在yield后面的那个表达式的值，作为返回对象的value属性的值。当下一次调用next方法时，再继续往下执行，直到遇到下一个yield语句。如果没有再遇到新的yield语句，就一直运行到函数结束，将return语句后面的表达式的值，作为value属性的值，如果该函数没有return语句，则value属性的值为undefined。

当第一次调用 **sum.next()** 时 返回的a变量值是5 + 3，同理第二次调用 **sum.next()** ，a变量值是8 +3，知道循环执行结束，返回**done:true**标识。

大家有没有发现个问题，koa 中 generator 的用法与上述 demo 演示的用法有非常大得差异，那是因为 koa 中的 generator 使用了 [co](https://www.npmjs.com/package/co) 进行了封装。

严重推荐阅读阮一峰老师写的 [《ES6入门》](http://es6.ruanyifeng.com/#docs/generator)，有更详细的描述，我们重点来了解下 co 。