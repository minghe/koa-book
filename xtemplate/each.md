{% raw %}

# xtemplate 中的循环语法

## 数组遍历

比如有个菜单，对应的数据是一个数组，{list:["女装","男装","童装"]}，模板内容如下：

    <ul>
        {{#each(list)}}
            <li>{{this}}</li>
        {{/each}}
    </ul>

使用each块状命令，可以遍历一个数组。{{this}}变量指向当前索引的值。

有时我们需要获取遍历当前的索引值，如何处理呢？

    <ul>
        {{#each(list)}}
            <li>{{xindex}}.{{this}}</li>
        {{/each}}
    </ul>

通过{{xindex}}可以获取索引值，从0开始。

this和xindex我们称之为xtemplate的关键字，数据Object字段定义时键名请避免使用。

xtemplate的强大之处在于很多js的原生方法可以在模板中使用，比如数组的方法。

    <ul>
        {{#each(list.slice(1))}}
            <li>{{xindex}}.{{this}}</li>
        {{/each}}
    </ul>

但是严重不建议在模板上编写太复杂的业务逻辑（有些模板过于强大，强大到可以在模板里面直接写js逻辑），这样很容易引起混乱，而且模板也不利于维护。

    
## 遍历二维数据
    
比如菜单数据：{list:[{"name":"女装","link":"http://xxx"},{"name":"男装","link":"http://xxx"},{"name":"童装","link":"http://xxx"}]}。

对应模板如下：

    <ul>
        {{#each(list)}}
            <li><a href="{{link}}">{{name}}</a></li>
        {{/each}}
    </ul>
    
无须使用{{this.name}} ，直接使用键名就可以获取字段数据。

问题来了，如果想要获取list级别的数据呢？

比如数据：{title:"kissy",list:[{"name":"女装","link":"http://xxx"},{"name":"男装","link":"http://xxx"},{"name":"童装","link":"http://xxx"}]}

each中需要用到 title 数据。    
    
## root 关键字

可以通过root关键字，获取数据根层（上面的demo中指list字段这一层）。

    <ul>
        {{#each(list)}}
            <li><a href="{{link}}">{{name}}</a> {{root.title}}</li>
        {{/each}}
    </ul>

问题又来了，如果数据结构非常复杂，我们要获取的数据位于list上一层，但不在root层呢？

## 相对引用

    <ul>
        {{#each(list)}}
            <li><a href="{{link}}">{{name}}</a> {{../title}}</li>
        {{/each}}
    </ul>

可以使用 ../ 来获取上一层的数据。

## 遍历对象

假设我们要遍历的数据，不是数组，而是对象，如何处理呢？

比如数据：{"data":{"x":1,"y":2}}。

    {{#with(data)}}
    {{x}}+{{y}}={{x+y}}
    {{/with}}
    
{% endraw %}

    
    








