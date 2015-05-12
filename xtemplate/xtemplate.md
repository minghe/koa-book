# 几个模板引擎的对比

## 为什么需要模板

渲染html到页面中，在koa中可以这么干：

    app.get('/',function*(){
        this.body = "<p>'+title+'</p>";
    })

但问题很大，模板混入到js逻辑了，非常不利于维护，基于 mvc 模式，我们需要将html模板抽离到 view 中方便维护，这时候我们就需要一款模板渲染引擎。

## 为什么选择xtemplate

适用于 koa 的模板引擎选择非常多，比如 [jade](https://www.npmjs.com/package/koa-jade)、[ejs](https://www.npmjs.com/package/koa-ejs)、[nunjucks](https://www.npmjs.com/package/koajs-nunjucks)、[xtemplate](https://github.com/xtemplate/xtemplate) 等。

为什么选择 xtemplate 呢？

### 上手难度

jade 无疑是最独特，上手难度最高，特别是将 html 转成 jade 风格的代码，需要一些成本和适应，特别是空格敏感，经常引起模块渲染报错：

    body
        h1 Jade - node template engine
        #container.col
          if youAreUsingJade
            p You are amazing
          else
            p Get on it!

但 jade 在 express 中拥有广泛的群众基础，所以从 express 转到 koa，jade 是个不错的选择，但我一直认为 jade 不是最佳的模板选择。

ejs、nunjucks、xtemplate 的上手难度差不多，但ejs的扩展写法有些诡异，特别是 filter 的写法：

    <p><%=: users | first | capitalize %></p>

xtemplate 相对于 nunjucks 的优势是，模板的逻辑写法体验更接近js（ejs的逻辑表达也很接近js），上手更为简单，来看下同样一个 if 判断的写法差异。

xtemplate:

    {{#if(variable===0)}}
      It is true    
    {{/if}}

nunjucks:

    {% if variable == 0 %}
      It is true
    {% endif %}

所以上手难度：xtemplate < nunjucks < ejs < jade 。

### 功能的强大度

四款模板引擎必备几样功能都具备：变量、逻辑表达式、循环、layout、include、宏、扩展等。

nunjucks 无疑是功能最全面的模板引擎，xtemplate 拥有 nunjucks 大部分的特性，除了filter，但 xtemplate 拥有非常强悍的拓展性：

    xtpl.addCommand('money',function(scope, option){
        var money = option.params[0];
        if(typeof money !== 'number') return '';
        //金额除以100（接口返回的金额都是以分为单位，转成元）
        var s = Number(money)/100;
        return s;
    })
    
模板中使用：

    {{money(10000)}}
    
nunjucks 与 xtemplate 都拥有实用的宏定义功能：

    {{#macro("test","param", default=1)}}
        param is {{param}} {{default}}
    {{/macro}}
    
模板中使用：
    
    {{macro("test","2")}}
    
输出内容：

    param is 2 1

jade 中的 Mixins，ejs 中的 function 也可以实现类似的抽取公用html代码块的目的 。

如果真要分个高下：nunjucks > xtemplate > jade > ejs 。

### 是否支持前后端混用

jade 直接淘汰，相信在前端js领域一般不会选择 jade 来渲染。

nunjucks 也使用的比较少（ejs其实也少），更多人会选择使用 handlebars 。

xtemplate 目前在阿里的系统中前后端混用中已经得到论证，节约了模板前后端转换的时间。

事实上是否支持前后端混用不是决定性因素，特别是在 angularjs 盛行的年代。

### 性能考量

它们都很快，其实性能都不是问题，真要较真的话，xtemplate 会更优秀些，可以看 [xtemplate benchmark](https://github.com/xtemplate/xtemplate/blob/master/benchmark/result/2014-09-22-benchmark.md)。

个人觉得性能不是决定性因素。

基于上述几点考虑，推荐使用 xtemplate 。