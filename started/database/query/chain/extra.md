## extra

`extra`方法可以用于`CURD`查询，例如：

```php
Db::name('user')
    ->extra('IGNORE')
    ->insert(['name' => 'think']);

Db::name('user')
    ->extra('DELAYED')
    ->insert(['name' => 'think']);

Db::name('user')
    ->extra('SQL_BUFFER_RESULT')
    ->select();
```



