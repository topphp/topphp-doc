## 删除数据

```php
// 根据主键删除
Db::table('think_user')->delete(1);
Db::table('think_user')->delete([1,2,3]);

// 条件删除    
Db::table('think_user')->where('id',1)->delete();
Db::table('think_user')->where('id','<',10)->delete();
```

最终生成的SQL语句可能是：

```php
DELETE FROM `think_user` WHERE  `id` = 1 
DELETE FROM `think_user` WHERE  `id` IN (1,2,3) 
DELETE FROM `think_user` WHERE  `id` = 1 
DELETE FROM `think_user` WHERE  `id` < 10
```

> `delete`方法返回影响数据的条数，没有删除返回 0

如果不带任何条件调用`delete`方法会提示错误，如果你确实需要删除所有数据，可以使用

```php
// 无条件删除所有数据
Db::name('user')->delete(true);
```

最终生成的SQL语句是（删除了表的所有数据）：

```php
DELETE FROM `think_user`

```

> 一般情况下，业务数据不建议真实删除数据，系统提供了软删除机制（模型中使用软删除更为方便）。

```php
// 软删除数据 使用delete_time字段标记删除
Db::name('user')
	->where('id', 1)
	->useSoftDelete('delete_time',time())
    ->delete();
```

实际生成的SQL语句可能如下（执行的是`UPDATE`操作）：

```php
UPDATE `think_user`  SET `delete_time` = '1515745214'  WHERE  `id` = 1
```

`useSoftDelete`方法表示使用软删除，并且指定软删除字段为`delete_time`，写入数据为当前的时间戳。

