{% raw %}
# xtemplate 自定义命令

xtemplate 拥有强大的拓展性，你可以自己定义行内命令和块状命令。

## 自定义行内命令

比如我们可以定义个常用的金额格式化命令 money()：

    XTemplate.addCommand('money',function(scope, option){
        return moneyFormat(option.params[0],option.params[1]);
    });
    
使用addCommand()方法注册自定义命令，如果是 XTemplate.addCommand() 注册的是全局命令，注册局部命令：

    var xtpl = new XTemplate(tpl);
    xtpl.addCommand();
    xtpl.render({});
    
局部命令只会对 XTemplate 的实例有效。

addCommand() 第一个参数为命令名称，第二个参数为命令逻辑函数。

option.params 即外部传参的值，比如 {{money(price,2)}},option.params[0] 指向price数据，option.params[1]=2。

## 自定义块状命令

    XTemplate.addCommand('msg', function (scopes, option,buffer) {
        html = '<div class="msg">'+option.params[0]+'</div>';
        return option.fn(scopes,buffer).write(html);
    });
    
此命令会向指定的input后追加一个消息容器。
    
模板内容：

    {{#msg(text)}}
        <input name="name" type="input">
    {{/msg}}
    
自定义块状命令会复杂些。需要使用 buffer 参数，用于处理模板内容。

option.fn(scopes,buffer) 返回的是 buffer （带有html内容），使用write()可以追加内容。

{% endraw %}
