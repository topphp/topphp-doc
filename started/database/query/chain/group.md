## group

`GROUP`方法通常用于结合合计函数，根据一个或多个列对结果集进行分组 。

`group`方法只有一个参数，并且只能使用字符串。

例如，我们都查询结果按照用户id进行分组统计：

```php
Db::table('user')
    ->field('user_id,username,max(score)')
    ->group('user_id')
    ->select();
```

生成的SQL语句是：

```php
SELECT user_id,username,max(score) FROM score GROUP BY user_id

```

也支持对多个字段进行分组，例如：

```php
Db::table('user')
    ->field('user_id,test_time,username,max(score)')
    ->group('user_id,test_time')
    ->select();
```

生成的SQL语句是：

```php
SELECT user_id,test_time,username,max(score) FROM user GROUP BY user_id,test_time
```



