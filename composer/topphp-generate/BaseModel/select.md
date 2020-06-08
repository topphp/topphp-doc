## 查询与链式操作

使用了`TopPHP`的`BaseModel`类，查询的时候推荐使用`queryChain`方法进行查询构造，通常我们使用`ThinkPHP`构造查询时，都是像如下的方式调用：

```php
$where = [
    'name' => 'thinkphp'
];
$user = new UserDao;
$user->where($where)->select();
```

如果使用`TopPHP`提供的`BaseModel`类，链式操作就需要以`queryChain`方法开头：

```php
$where = [
    'name' => 'thinkphp'
];
$user = new UserDao;
$user->queryChain($where)->select();
```

这样是为了兼容`BaseModel`类的自动软删除功能，默认使用`queryChain`构造的查询，不使用`ThinkPHP`的软删除`use SoftDelete;`也会自动过滤软删除数据。

> queryChain \( '查询条件', '\[ and \]是否or查询', '\[ excludeSoft \]软删除过滤判断' \);

`queryChain`支持`or`查询，支持软删除过滤判断，软删除过滤判断参数如下：

| queryType | 枚举常量 | 说明 |
| :--- | :--- | :--- |
| excludeSoft | MethodEnum::EXCLUDE\_SOFT | 排除软删除 |
| withSoft | MethodEnum::WITH\_SOFT | 包含软删除 |
| onlySoft | MethodEnum::ONLY\_SOFT | 仅查询软删除 |

`queryChain`的`$queryType`参数默认`excludeSoft`，自动排除软删除数据。

### 查询条件

`TopPHP`提供的`BaseModel`类，大部分方法的`where`查询条件如非特殊说明都支持传入主键`id`、关联数组、表达式形式的索引数组（多条件的可以是二维数组）、闭包。

主键id示例：

```php
$user = new UserDao;
$user->queryChain(1)->find();
```

关联数组示例：

```php
$user = new UserDao;
$where = [
    'name' => 'thinkphp'
];
$user->queryChain($where)->select();
```

表达式形式的一围数组示例：

```php
$user = new UserDao;
$where = ["id", ">", 10];
$user->queryChain($where)->select();
```

多个条件可以使用表达式形式的二维数组示例：

```php
$user = new UserDao;
$ids = [1, 3, 9];
$where = [
    ["id", "in", $ids],
    ["id", ">", 10]
];
$user->queryChain($where, "or")->select();
```

闭包示例：

```php
$user = new UserDao;
$ids = [1, 3, 9];
$where = function ($query) use ($ids) {
    $query->where("id", "in", $ids);
    $query->whereOr("id", ">", 10);
};
$user->queryChain($where)->select();
```
