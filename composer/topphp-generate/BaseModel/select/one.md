## 查询一条

> findField \( '查询条件', '查询字段', '\[ and \]是否or查询', '\[ false \]是否包含软删除数据', '\[ false \]是否是select查询' \);

查询一条数据可以使用支持字段筛选的`findField`方法：

```php
$user = new UserDao;
$user->findField(1);
```

> `findField`的查询条件支持传入主键`id`、关联数组、表达式形式的索引数组（多条件的可以是二维数组）、闭包。

我们可以通过第二个参数`$field`来筛选字段：

```php
$where = [
    'name' => 'thinkphp'
];
$user = new UserDao;
$user->findField($where, ["id", "email"]);
```

`$field`参数支持字符串形式：

```php
$where = [
    'name' => 'thinkphp'
];
$user = new UserDao;
$user->findField($where, "id,email as em");
```