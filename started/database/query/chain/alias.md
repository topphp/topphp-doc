## alias

`alias`用于设置当前数据表的别名，便于使用其他的连贯操作例如join方法等。

示例：

```php
Db::table('think_user')
->alias('a')
->join('think_dept b ','b.user_id= a.id')
->select();
```

最终生成的SQL语句类似于：

```php
SELECT * FROM think_user a INNER JOIN think_dept b ON b.user_id= a.id

```

可以传入数组批量设置数据表以及别名，例如：

```php
Db::table('think_user')
->alias(['think_user'=>'user','think_dept'=>'dept'])
->join('think_dept','dept.user_id= user.id')
->select();
```

最终生成的SQL语句类似于：

```php
SELECT * FROM think_user user INNER JOIN think_dept dept ON dept.user_id= user.id
```



