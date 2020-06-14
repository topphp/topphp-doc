## replace

`replace`方法用于设置`MySQL`数据库`insert`方法或者`insertAll`方法写入数据的时候是否适用`REPLACE`方式。

```php
Db::name('user')
    ->replace()
    ->insert($data);
```



