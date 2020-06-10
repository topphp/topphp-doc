## 查询一条

> findField \( '查询条件', '查询字段', '\[ and \]是否or查询', '\[ false \]是否包含软删除数据', '\[ false \]是否是select查询' \);

查询一条数据可以使用支持字段筛选的`findField`方法：

```php
$user = new UserDao;
$user->findField(1);
```

以上方式将获取到`id`为`1`的所有数据，但不包含模型`Dao`的隐藏字段`$hidden`属性中设置了的字段。

> `findField`的查询条件支持传入主键`id`、关联数组、表达式形式的索引数组（多条件的可以是二维数组）、闭包。

我们可以通过第二个参数`$field`来筛选字段：

```php
$user = new UserDao;
$where = [
    'name' => 'thinkphp'
];
$user->findField($where, ["id", "email"]);
```

还可以通过数组形式定义别名：

```php
$user = new UserDao;
$where = [
    'name' => 'thinkphp'
];
$user->findField($where, ["id", "password" => "pwd"]);
```

`$field`参数支持字符串形式：

```php
$user = new UserDao;
$where = ['name', 'like', 'thinkphp'];
$user->findField($where, "id,password as pwd");
```

还可以使用SQL函数：

```php
$user = new UserDao;
$where = [
    ['name', 'like', 'thinkphp'],
    ['create_time', '<', '2020-06-03 18:13:17']    
];
$user->findField($where, "SUM(score) as sc");
```

> 当`findField`的筛选字段获取的是一个字段时将直接返回该字段的值。

```php
$user = new UserDao;
$user->findField(1, "password");
```

上面将直接返回`password`字段的值，`findField`实现了`ThinkPHP`的`find`查询与`value`查询的一个结合。所以`findField`方法是一个常用来查询单条数据的字段信息的方法。

> 如果你在模型`Dao`的隐藏字段`$hidden`属性中设置了某个字段隐藏，那么`$field`筛选字段时你是获取不到该字段的，需要获取该字段可以使用`getModelData`方法来获取。



