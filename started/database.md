## 数据库

新版的数据库和模型操作已经独立为[`ThinkORM`](https://github.com/top-think/think-orm)库，默认安装应用的时候会自动安装，如果你不需要使用该`ORM`库的话，可以单独卸载`topthink/think-orm`后安装其它的`ORM`库。

## 主要特性：

* 基于PDO和PHP强类型实现
* 支持原生查询和查询构造器
* 自动参数绑定和预查询
* 简洁易用的查询功能
* 强大灵活的模型用法
* 支持预载入关联查询和延迟关联查询
* 支持多数据库及动态切换
* 支持`MongoDb`
* 支持分布式及事务
* 支持断点重连
* 支持JSON查询
* 支持数据库日志
* 支持`PSR-16`缓存及`PSR-3`日志规范

> 要使用Db类必须使用门面方式（`think\facade\Db`）调用

## 开发指南

关于`ThinkORM`更多的内容可以参考[`ThinkORM`开发指南](https://www.kancloud.cn/manual/think-orm/content)文档。

