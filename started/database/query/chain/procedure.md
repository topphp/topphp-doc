## procedure

`procedure`方法用于设置当前查询是否为存储过程查询，用法如下：

```php
$resultSet = Db::procedure(true)
    ->query('call procedure_name');
```



