## having

`HAVING`方法用于配合group方法完成从分组的结果中筛选（通常是聚合条件）数据。

`having`方法只有一个参数，并且只能使用字符串，例如：

```php
Db::table('score')
    ->field('username,max(score)')
    ->group('user_id')
    ->having('count(test_time)>3')
    ->select(); 
```

生成的SQL语句是：

```php
SELECT username,max(score) FROM score GROUP BY user_id HAVING count(test_time)>3
```



