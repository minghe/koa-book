# 什么是 redis ？

![./1.jpg](./1.jpg)

[Redis](http://www.redis.cn/) 是一个开源的内存数据库服务器， 相对于传统数据，它快的惊人，是高性能Key-Value存储系统，在商业应用领域常用于缓存用途（二级存储）。

阿里云基于 redis 做了个 [KVStore](http://www.aliyun.com/product/kvstore) 的云服务产品，推荐了解下，除了贵，其他都很好...

其实人们低估了 redis，除了缓存用途外，redis 一样可以像 mySQL 一样作为主存储，redis 拥有独一无二的数据模型，它的 value 有 5 种不同类型，可以覆盖大部分的数据结构场景。

这一章只会重点讲解 redis 结合 node 常见的几种用法，更详细的 redis 内容，建议大家购买[《 redis 实战》](https://s.taobao.com/search?initiative_id=tbindexz_20151118&ie=utf8&spm=a21bo.7724922.8452-taobao-item.2&sourceId=tb.index&search_type=item&ssid=s5-e&commend=all&imgfile=&q=redis%E5%AE%9E%E6%88%98&suggest=history_1&_input_charset=utf-8&wq=redis&suggest_query=redis&source=suggest)。



