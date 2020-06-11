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

### selectEnd 查询最后一条（或后Limit条）

> selectEnd \( '查询条件', '\[ 1 \]Limit筛选', '\[ and \]是否or查询' \);

```php
$user = new UserDao;
$where = [
    'name' => 'topphp'
];
$user->selectEnd($where);
```

上面将返回数据库中最后一次出现`name`值为`topphp`的那条数据。

> `selectEnd`默认的排序条件为主键`id`降序，`create_time`降序，`update_time`降序，如果三个字段都不存在，将默认根据数据表第一个出现的字段降序。

支持第二个参数`$limit`获取后 N 条数据：

```php
$user = new UserDao;
$where = [
    'name' => 'topphp'
];
$user->selectEnd($where, 10);
```

上面的数据将返回满足条件的后`10`条数据。

> `selectEnd`如果是`limit`一条数据，默认返回该条数据键值对一围数组，如果是`limit`多条，将返回二维数组。

### selectRand 满足条件的数据随机返回（或随机取Limit条）

> selectRand \( '查询条件', '\[ 1 \]Limit筛选', '\[ and \]是否or查询' \);

随机返回一条：

```php
$user = new UserDao;
$where = [
    'name' => 'topphp'
];
$user->selectRand($where);
```

随机返回limit条：

```php
$user = new UserDao;
$where = [
    'name' => 'topphp'
];
$user->selectRand($where, 10);
```