## Limit 查询

### selectFirst 查询首条（或前Limit条）

> selectFirst \( '查询条件', '\[ 1 \]Limit筛选', '\[ and \]是否or查询' \);

```php
$user = new UserDao;
$where = [
    'name' => 'topphp'
];
$user->selectFirst($where);
```

上面将返回数据库中第一次出现`name`值为`topphp`的那条数据。

> `selectFirst`默认的排序条件为主键`id`升序，`create_time`升序，如果两个字段都不存在，将默认查询数据表最早的数据。

支持第二个参数`$limit`获取前 N 条数据：

```php
$user = new UserDao;
$where = [
    'name' => 'topphp'
];
$user->selectFirst($where, 10);
```

上面的数据将返回满足条件的前`10`条数据。

> `selectFirst`如果是`limit`一条数据，默认返回该条数据键值对一围数组，如果是`limit`多条，将返回二维数组。

