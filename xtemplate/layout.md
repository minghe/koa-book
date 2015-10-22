# 模板中的layout

layout 在服务器端模板中是非常重要的概念（前端模板很少会用），指的是页面的结构布局抽离，比如每个页面的head部分都是一样的，页尾也是一样的，这时候我们就可以定义个 layout （layout也是.xtpl模板），每个页面都继承于这个layout。

### 定义个 layout

在 view 目录下创建 layout 目录，放个 default.xtpl ，内容如下：

    <!doctype html>
    <html>
    <head>
        <meta name="charset" content="utf-8" />
        <title>{{title}}</title>
        <script src="http://cdn.staticfile.org/jquery/2.1.1-rc2/jquery.min.js"></script>
        <script src="http://cdn.staticfile.org/twitter-bootstrap/3.3.1/js/bootstrap.min.js"></script>
        <link rel="stylesheet" href="http://cdn.staticfile.org/twitter-bootstrap/3.3.1/css/bootstrap-theme.min.css">
        {{{block ("head")}}}
    </head>
    <body>
        {{{block ("body")}}}
    </body>
    </html>
    

可以看到，我们将页面基础结构抽离出来了，留意 block ("head") ，block 是xtemplate指令，用于继承于 layout 的页面覆盖 layout 中的 block 占位。

（ **三个刮号** 表示支持html变量，防止转译。）

假设 test.xtpl 继承于 layout/default.xtpl ：

    {{extend ("./layout/layout")}}
    
    {{#block ("head")}}
        <style>
    
        </style>
    {{/block}}
    
    {{#block ("body")}}
    <p>这是个 koa 的测试页面</p>
    {{/block}}

使用 **extend()** 指令可以继承于指定的xtpl模块（被继承的模板就是 layout）。

    {{#block ("head")}}
     <style>
    
     </style>
    {{/block}}
    
block 块状指令用于定义 block 占位的 html 内容，这样我们就可以在通用的 layout，定制页面的业务html内容。

xtemplate的指令分为行内指令与块状指令，比如 block("") 就是行内指令，而 {{#block("")}} 是块状指令，以 **#**开头，并需要{{/block}}结束标识。

最后页面的渲染结果是：


    <!doctype html>
    <html>
    <head>
        <meta name="charset" content="utf-8" />
        <title></title>
        <script src="http://cdn.staticfile.org/jquery/2.1.1-rc2/jquery.min.js"></script>
        <script src="http://cdn.staticfile.org/twitter-bootstrap/3.3.1/js/bootstrap.min.js"></script>
        <link rel="stylesheet" href="http://cdn.staticfile.org/twitter-bootstrap/3.3.1/css/bootstrap-theme.min.css">
        <style>
    
        </style>
    </head>
    <body>
        <p>这是个 koa 的测试页面</p>
    </body>
    </html>
    
被继承的 layout 共享页面的上下文数据，比如 layout 有个占位变量 {{title}}，页面渲染时 this 上下文存在 title，依旧可以正常替换。