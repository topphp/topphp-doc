## 事务操作

使用事务处理的话，需要数据库引擎支持事务处理。比如`MySQL`的`MyISAM`不支持事务处理，需要使用`InnoDB`引擎。

最简单的方式是使用`transaction`方法操作数据库事务，当闭包中的代码发生异常会自动回滚，例如：

```php
Db::transaction(function () {
    Db::table('think_user')->find(1);
    Db::table('think_user')->delete(1);
});
```

也可以手动控制事务，例如：

```php
// 启动事务
Db::startTrans();
try {
    Db::table('think_user')->find(1);
    Db::table('think_user')->delete(1);
    // 提交事务
    Db::commit();
} catch (\Exception $e) {
    // 回滚事务
    Db::rollback();
}
```

> 注意在事务操作的时候，确保你的数据库连接使用的是同一个。

可以支持MySQL的`XA`事务用于实现全局（分布式）事务，你可以使用：

```php
Db::transactionXa(function () {
    Db::connect('db1')->table('think_user')->delete(1);
    Db::connect('db2')->table('think_user')->delete(1);
}, [Db::connect('db1'),Db::connect('db2')]);
```

> 要确保你的数据表引擎为`InnoDB`，并且开启XA事务支持。



