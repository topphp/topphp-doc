## 根据字段筛选

### selectSameField 查询某个字段的值相同的数据

> selectSameField \( '主键id', '字段名', '查询排序', '\[ false \]是否返回model对象' \);

`selectSameField`方法用于查询同一张表指定字段值相同的数据，支持结果排序。

例如查询`id`为`1`的这条数据`nickname`字段值相同的数据：

```php
$user = new UserDao;
$user->selectSameField(1, "nickname");
```

生成的SQL语句为：

```php
SELECT * FROM `topphp_user` WHERE `nickname` IN (SELECT `nickname` FROM `topphp_user` WHERE `id` = 1) AND ( `delete_time` IS NULL OR `delete_time` = 0 )
```

`selectSameField`返回的是相同数据的二维数组。支持第四个参数`$isModel`返回`model`对象，可以链式调用分页等`ThinkPHP`的链式操作。

### selectRepeat 查询指定字段值**重复**的记录

> selectRepeat \( '查询条件', '字段名数组', '查询排序', '\[ and \]是否or查询', '\[ false \]是否返回model对象' \);

例如查询数据表中`nickname`字段值重复的数据：

```php
$user = new UserDao;
$user->selectRepeat("*", ["nickname"]);
```

生成的SQL语句为：

```php
SELECT * FROM `topphp_user` WHERE ( `delete_time` IS NULL OR `delete_time` = 0 )  AND ( (nickname) IN (SELECT nickname FROM topphp_user GROUP BY nickname HAVING COUNT(*)>1) )
```

`selectRepeat`返回的是重复数据的二维数组。上面的查询将会查询出数据表中所有`nickname`字段值有重复的记录。

解释一下`selectRepeat`方法所指的重复的含义：比如`id`为`1`的`nickname`为`zhangsan`，同时`id`为`5`的`nickname`也为`zhangsan`，那么这两条数据就会被查询出来，同时`id`为`3`的`nickname`为`lisi`，同时`id`为`7`的`nickname`也为`lisi`，那么这两条也会被查询出来，当我们在`$where`限定条件为`["nickname" => "zhangsan"]`则只会返回`id`为`1`和`5`两条数据了。

所以**查询重复的记录具体返回是通过`$where`查询条件来控制的**。


### selectNoRepeat 查询指定字段值**不重复**的记录



