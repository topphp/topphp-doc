## 查询一条

### find 查询

> findField \( '查询条件', '查询字段', '\[ and \]是否or查询', '\[ false \]是否包含软删除数据', '\[ false \]是否是select查询' \);

查询一条数据可以使用支持字段筛选的`findField`方法：

```php
$user = new UserDao;
$user->findField(1);
```

以上方式将获取到`id`为`1`的所有数据，但不包含模型`Dao`的隐藏字段`$hidden`属性中设置了的字段。

> `findField`的查询条件不能为空，支持传入主键`id`、关联数组、表达式形式的索引数组（多条件的可以是二维数组）、闭包。

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

> `findField`默认返回当前数据的一围键值对数组，当`findField`的筛选字段获取的仅是单一字段时将直接返回该字段的值。

```php
$user = new UserDao;
$user->findField(1, "password");
```

上面将直接返回`password`字段的值，`findField`实现了`ThinkPHP`的`find`查询与`value`查询的一个结合。所以`findField`方法是一个常用来查询单条数据的字段信息的方法。

> 如果你在模型`Dao`的隐藏字段`$hidden`属性中设置了某个字段隐藏，那么`$field`筛选字段时你是获取不到该字段的，需要获取该字段可以使用`getModelData`方法来获取。

`findField`的第三个参数是查询条件的`or`查询，如果你看了前面的文档，就不会生疏：

```php
$user = new UserDao;
$ids  = [1, 6, 10];
$where = [
    ["id", "in", $ids],
    ["id", ">", 32]
];
$user->findField($where, "*", "or");
```

当然`findField`仅会获取上面满足条件的第一条数据，因为`findField`是仅查询单条的方法。

> 使用`$field`筛选字段时，不传、传空数组、或者传`string`类型的`*`都视为获取不包含隐藏字段的所有字段。

`findField`的第四个参数是提供给你查询软删除数据的方式：

```php
$user = new UserDao;
$ids  = [1, 6, 10];
$where = [
    ["id", "in", $ids],
    ["id", ">", 32]
];
$user->findField($where, "*", "or", true);
```

> 第四个`$withSoft`参数默认`false`不包含软删除数据，如果你需要查询包含软删除的数据，请传`true`。

如果你需要返回一个二维数组形式的数据，即相当于使用`ThinkPHP`的`select`来查询一条数据，可以传入第五个布尔值参数`$isSelect`：

```php
$user = new UserDao;
$user->findField(1, "*", "and", false, true);
```

上面的查询将会返回一个二维的数据形式数据，如果查询不到，也将会返回空数组。当然这种情况的需求可能少之又少，`TopPHP`仅仅提供你这么一种返回方式，实际看需求。

> `findField`方法无论你使用那种方式，返回的都仅是一条数据，所以`findField`方法仅适用于对于单条数据的查询操作。

其实如果你有`findField`方法第五个参数的那种需求，是可以直接使用下面的`selectOne`方法来实现的。

### select 查询

> selectOne \( '查询条件', '查询排序', '\[ and \]是否or查询', '\[ false \]是否是find查询' \);

`selectOne`查询默认通过`ThinkPHP`的`select`方法进行查询，但也仅会返回一条数据：

```php
$user = new UserDao;
$user->selectOne(1);
```

> `selectOne`默认返回二维数组形式的数据，且会自动过滤软删除数据，如果查询不到将返回空数组。如果你想查询软删除数据，请使用`findField`方法。

`selectOne`不支持过滤字段，但支持模型`Dao`的隐藏字段`$hidden`属性的设置隐藏规则。`selectOne`支持`order`排序查询：

```php
$user = new UserDao;
$ids  = [1, 6, 10];
$where = ["id", "in", $ids];
$user->selectOne($where, ["create_time" => "desc"]);
```

上面的查询将返回`id`在`1`、`6`、`10`这三条数据中创建时间降序以后的第一条数据。`selectOne`的第二个参数`$order`支持数组形式定义、和原生SQL语句排序。

多个字段的数组形式可以如下定义：

```php
$user = new UserDao;
$ids  = [1, 6, 10];
$where = ["id", "in", $ids];
$user->selectOne($where, ["score", "create_time" => "desc"]);
```

上面的查询将会生成类似如下SQL语句：

```php
SELECT * FROM `topphp_user` WHERE  `id` IN (1,6,10)  AND (  `delete_time` IS NULL OR `delete_time` = 0 ) ORDER BY `score`,`create_time` DESC LIMIT 1
```

如果你需要将`score`升序：

```php
$user = new UserDao;
$ids  = [1, 6, 10];
$where = ["id", "in", $ids];
$user->selectOne($where, ["score" => "asc", "create_time" => "desc"]);
```

如果都是升序：

```php
$user = new UserDao;
$ids  = [1, 6, 10];
$where = ["id", "in", $ids];
$user->selectOne($where, ["score", "create_time"]);
```

> `$order`参数如果没有指定`desc`或者`asc`排序规则的话，默认为`asc`。

如果你想使用原生的sql排序可以直接传入原生sql字符串：

```php
$user = new UserDao;
$ids  = [1, 6, 10];
$where = ["id", "in", $ids];
$user->selectOne($where, "score,create_time desc");
```
> 如果你需要在`$order`参数中使用`mysql`函数的话，必须使用字符串的方式：

```php
$user = new UserDao;
$where = ["id", ">", 10];
$user->selectOne($where, "field(name,'thinkphp','topphp','onethink')");
```

第三个`$isOr`参数前面已经遇到过，在此不做赘述，第四个`$isFind`参数表示是否是`ThinkPHP`的`find`查询：

```php
$user = new UserDao;
$user->selectOne(1, [], "and", true);
```

上面的方式将直接返回一围数组形式的结果，如果查询不到，也会返回一个空数组，而不是`null`。

### 我们推荐的用法是：

* 如果你需要筛选字段、查询一条数据信息或者是单一字段信息，亦或是需要查询被软删除的数据，请使用`findField`
* 如果你需要查询一条信息无论是否查询到都返回数组，亦或是有排序后查询的需求，请使用`selectOne`


