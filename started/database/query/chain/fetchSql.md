## fetchSql

`fetchSql`用于直接返回SQL而不是执行查询，适用于任何的CURD操作方法。 例如：

```php
echo Db::table('user')->fetchSql(true)->find(1);
```

输出结果为：

```php
SELECT * FROM user where `id` = 1
```

> 对于某些NoSQL数据库可能不支持fetchSql方法



