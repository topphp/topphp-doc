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

需要查询所有，提供三种方式：

```php
$user = new UserDao;
$user->queryChain('*')->select(); // 字符串 '*'
$user->queryChain([])->select(); // 空数组
$user->queryChain()->select(); // 不传
```

> 查询条件不传、传空数组、或者传`string`类型的`*`在链式操作无其他`where`条件的情况下都视为查询所有。

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

原生表达式形式的SQL：

```php
$user = new UserDao;
$where = ["id", "exp", ">score"];
$user->queryChain($where)->select();
```

> `exp`查询的条件不会被当成字符串，所以后面的查询条件可以使用任何SQL支持的语法，包括使用函数和字段名称。

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

原生SQL字符串条件查询：

```php
$user = new UserDao;
$where = 'id > 0 AND name LIKE "thinkphp%"';
$user->queryChain()->whereRaw($where)->select();
```

原生SQL字符串条件查询（使用参数绑定）：

```php
$user = new UserDao;
$where = 'id > :id AND name LIKE :name ';
$user->queryChain()->whereRaw($where, ['id' => 0, 'name' => 'thinkphp%'])->select();
```

> 事实上`queryChain`仅是一个链式操作查询的开始，后面的链式操作与`ThinkPHP`的链式操作并无异处，你可以使用任何`ThinkPHP`支持的链式操作;

需要注意的是，以下所有快捷方法的`where`都不支持直接传空数组来识别为查询所有，需要查询所有请统一传入字符串`*`。

* findField 【查询字段值】
* selectOne 【查询一条】
* selectAll 【查询所有】
* selectList 【查询List】
* selectColumn 【查询列】
* selectSort 【查询排序】
* selectFirst 【查询首条数据】
* selectEnd 【查询最后一条数据】
* selectRand 【满足条件的数据随机返回】
* selectRepeat 【查询指定字段值重复的记录】
* selectNoRepeat 【查询指定字段值不重复的记录】
* selectFieldInSet 【FIND_IN_SET查询】
* selectFieldInList 【FIND_IN_SET查询反向】
* selectJoin 【innerJoin联查】
* selectLeftJoin 【leftJoin联查】
* selectRightJoin 【rightJoin联查】
* selectFullJoin 【fullJoin联查】
* selectChild 【一对多子查询】

### 我们推荐的用法是：

* 使用`queryChain()`进行链式操作，查询所有，直接不传`where`条件即可。
* 使用其他的方法时，如非特殊说明，查询所有的条件，统一使用通配符字符串`*`。

