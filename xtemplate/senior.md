{% raw %}

# xtemplate 高级语法

以下语法虽然不常用，但对优化模板代码有莫大帮助。

## set() 设置变量

有时判断逻辑特别复杂，为了让代码更利于阅读，好的版本是提取设置变量。

比如：

    {{#if(x===1&&y===2)}}
       {{#set(z=3)}}
    {{/if}}
    {{#if(z===3)}}
       等价于 x===1&&y===2
    {{/if}}
    
## macro() 宏的定义

有时我们模板中会多次出现一个结构，比如一个表单，每个input下面有个消息容器：

    <div class="auth-msg">该字段必填</div>
    
多处定义不利于维护，我们希望提取这个div，如何处理呢？

借助xtemplate的宏就可以实现：

    {{#macro("msg","text")}}
        <div class="auth-msg">{{msg}}</div>
    {{/macro}}
    
    <input type="text" name="name" value="">
    {{macro("msg","名字不可以为空")}}
    
留意macro命令比较特殊，第一个参数定义宏的名称，这里我们命名为msg，调用时就是 {{macro("msg")}} 。后面的参数都是自定义的传参。


## include() 引用子模板

x.xtpl 与 y.xtpl 同级目录，y.xtpl 引用子模板x.tpl：

    {{{include("x")}}}
    
留意是{{{}}}。

{% endraw %}






