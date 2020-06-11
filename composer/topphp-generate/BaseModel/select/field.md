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

### selectRepeat 查询指定字段值 重复 的记录

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

所以**查询重复的记录具体返回是通过**`$where`**查询条件来控制的**。

### selectNoRepeat 查询指定字段值 不重复 的记录

> selectNoRepeat \( '查询条件', '字段名数组', '查询排序', '\[ and \]是否or查询', '\[ false \]是否返回model对象' \);

`selectNoRepeat`与`selectRepeat`是相反的存在，即只有当指定的字段存在唯一性时才会被查询出来。

```php
$user = new UserDao;
$user->selectNoRepeat("*", ["nickname"]);
```

生成的SQL语句为：

```php
SELECT * FROM `topphp_user` WHERE ( `delete_time` IS NULL OR `delete_time` = 0 )  AND ( (nickname) IN (SELECT nickname FROM topphp_user GROUP BY nickname HAVING COUNT(*)=1) )
```

解释一下`selectNoRepeat`方法所指的不重复的含义：即数据表中`id`为`10`的`nickname`为`topphp`，而在数据表中不会再出现其他的`id`数据`nickname`字段值是`topphp`了，那么`id`为`10`的这条数据就是不重复的数据。

### selectFieldInSet FIND_IN_SET查询

> selectFieldInSet \( '查询条件', '字段与值数组', '查询排序', '\[ and \]是否or查询', '\[ false \]是否返回model对象' \);

`selectFieldInSet`查询指定字段包含指定的值或字符串的数据集合。

应用场景：常用于判断类型或筛选权限等场景，例如 有个文章表里面有个type字段，它存储的是文章类型，有 1头条、2推荐、3热点、4图文等等，现在有篇文章他既是头条，又是热点，还是图文，type中以 1,3,4 的格式存储。我们就可以使用此方法进行查询所有type中有4的图文类型的文章。

```php
$user = new ArticleDao;
$user->selectFieldInSet("*", ["type" => 4]);
```

以上查询将会生成类似如下SQL：

```php
SELECT * FROM `topphp_article` WHERE ( `delete_time` IS NULL OR `delete_time` = 0 )  AND FIND_IN_SET(4, `type`)
```

> 需要注意的是：数据表的`type`字段值应该是`1,3,4`这种格式存储的，`selectFieldInSet`的第二个参数`$fieldAndVal`应为键值对形式`[field=>value]`其中`value`必须是`string`或`int`型。

如果你有查询出来分页的需求，可以使用`selectFieldInSet`的第五个参数`$isModel`来返回`model`模型对象进行链式操作调用`paginate`方法进行分页。具体操作方式前面的`查询所有`章节已经描述过，在此不做赘述。





