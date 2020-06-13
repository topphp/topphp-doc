## 关联查询

### 链式操作

> setBaseQuery \( '当前表别名', '当前表字段名数组', '关联规则', '\[ join \]关联方式' \);

名词解释：

* 主表：即当前表
* 联查表：即与当前表关联的其他数据表

参数说明：

```
1、$alias 当前表（以下称"主表"）别名，如 'order' 表，别名 'o'。
2、$field 当前表字段数组，如 'order' 表的字段 [field1,field2...]，如果需要查询所有字段直接传 '空数组' 或者是 通配符字符串 '*' 。
3、$join 关联规则，需要连接的表（以下称"联查表"），参数为数组形式，结构如下：

    ['联查表 (别名)', '联查表外键字段', ('主表主键字段'), ('连接类型')]

    上面的括号表示可选，如果你的主键与外键的字段名一致的话，可以省略第三个参数。
    以上第三个【主表主键字段】参数不传，默认使用【联查表外键字段】进行联查。
    第四个参数为【连接类型】，默认以setBaseQuery方法的【关联方式】为主，主要用于多张表不同连接类型的情况，优先级高于$type参数

    如果是多张表联查，可以使用二维数组形式，如：
    [['order_goods og', 'order_id', 'id'],...]

4、$type 关联方式，默认'join'方式，可选'join'，'leftJoin'，'rightJoin'，'fullJoin'。
```

假如我们有一个订单表和一个订单商品表，进行关联，表的结构大概是如下样子的：

![](/assets/join.png)

`order`表与`order_goods`表通过`order_id`进行关联，我们这个示例是一个一对多的数据关系，一笔订单下会有多个商品。

通常你使用`TopPHP`的`BaseModel`类进行链式操作时都要使用`queryChain`进行链式操作的开始，如果你想直接使用`ThinkPHP`的链式操作联查方式也是可以的：

```php
$order = new OrderDao;
$where = [
    "a.order_id" => 1,
];
$order->queryChain($where)
->alias('a')
->leftJoin('order_goods b','a.order_id = b.order_id')
->select();
```

但是`BaseModel`提供`setBaseQuery`方法来简化上面的联查写法：

```php
$order = new OrderDao;
$where = [
    "a.order_id" => 1,
];
$order->setBaseQuery("a", "*", ["order_goods b","order_id"], "leftJoin")
->where($where)
->select();
```

**需要注意的是**`setBaseQuery`**方法默认都是针对主表的数据查询，所以上面的**`*`**筛选出来的都是主表（即当前表的数据），如果你需要获取联查表的数据还需要链式操作通过**`field`**方法来筛选：**

```php
$order = new OrderDao;
$where = [
    "a.order_id" => 1,
];
$order->setBaseQuery("a", "*", ["order_goods b","order_id"], "leftJoin")
->field('b.*')
->where($where)
->select();
```

上面的写法获取到的数据结果就与第一种`queryChain`形式获取的结果一致了，生成的SQL语句都为：

```php
SELECT `a`.*,`b`.* FROM `topphp_order` `a` LEFT JOIN `topphp_order_goods` `b` ON `b`.`order_id`=`a`.`order_id` WHERE  `a`.`order_id` = 1
```

这也就会产生一个问题，就是两张表如果出现相同字段名的情况，后面的表会把前面的表字段值覆盖，有可能导致查询数据结果与实际数据不符，通常我们获取数据都是以某一张表的数据为主，所以`setBaseQuery`才会将两种过滤分开处理。

以主表数据为主我们就：

```php
$order = new OrderDao;
$where = [
    "a.order_id" => 1,
];
$join = ["order_goods b","order_id"];
$order->setBaseQuery("a", "*", $join, "leftJoin")
->field('b.goods_name')
->where($where)
->select();
```

以联查表数据为主我们就：

```php
$order = new OrderDao;
$where = [
    "a.order_id" => 1,
];
$join = ["order_goods b","order_id"];
$order->setBaseQuery("a", ["order_no", "order_price"], $join, "leftJoin")
->field('b.*')
->where($where)
->select();
```

如果是多张表联查也是一样的处理方式，`$join`参数要变为二维数组：

```php
$order = new OrderDao;
$where = [
    "a.order_id" => 1,
];
$join = [
    ["order_goods b", "order_id"],
    ["user c", "id", "user_id"]
];
$order->setBaseQuery("a", "*", $join, "leftJoin")
->field('b.goods_name,c.username')
->where($where)
->select();
```

上面将会生成类似如下SQL：

```php
SELECT `a`.*,`b`.`goods_name`,`c`.`username` FROM `topphp_order` `a` LEFT JOIN `topphp_order_goods` `b` ON `b`.`order_id`=`a`.`order_id` LEFT JOIN `topphp_user` `c` ON `c`.`id`=`a`.`user_id` WHERE ( `c`.`delete_time` IS NULL OR `c`.`delete_time` = 0 ) AND `a`.`order_id` = 1
```

> 注意：`setBaseQuery`方法也会默认过滤软删除数据，如果你的数据表中存在如`delete_time`这样的软删除字段，就会自动触发软删除筛选。像上面的查询，我的`order`表和`order_goods`表都不存在软删除字段，但是`user`表存在软删除字段了，所以你看到的查询中就会加入过滤`user`表软删除的语句。

如果你想构造如`order_goods`表与`order`表是`leftJoin`关系，`user`表与`order`表是`rightJoin`关系，那么就需要在`$join`的二维数组中加入第四个参数：

```php
$order = new OrderDao;
$where = [
    "a.order_id" => 1,
];
$join = [
    ["order_goods b", "order_id"],
    ["user c", "id", "user_id", "rightJoin"]
];
$order->setBaseQuery("a", "*", $join, "leftJoin")
->field('b.goods_name,c.username')
->where($where)
->select();
```

上面将会生成类似如下SQL：

```php
SELECT `a`.*,`b`.`goods_name`,`c`.`username` FROM `topphp_order` `a` LEFT JOIN `topphp_order_goods` `b` ON `b`.`order_id`=`a`.`order_id` RIGHT JOIN `topphp_user` `c` ON `c`.`id`=`a`.`user_id` WHERE ( `c`.`delete_time` IS NULL OR `c`.`delete_time` = 0 ) AND `a`.`order_id` = 1
```

还可以省略`setBaseQuery`的第四个`$type`参数，直接在`$join`数组中定义：

```php
$order = new OrderDao;
$where = [
    "a.order_id" => 1,
];
$join = [
    ["order_goods b", "order_id", "order_id", "leftJoin"],
    ["user c", "id", "user_id", "rightJoin"]
];
$order->setBaseQuery("a", "*", $join)
->field('b.goods_name,c.username')
->where($where)
->select();
```

这与上面一个示例生成的SQL语句是一样的。数组中定义关联方式的优先级要高于`setBaseQuery`的第四个`$type`参数。

我们也支持一维数组形式的定义：

```php
$order = new OrderDao;
$where = [
    "a.order_id" => 1,
];
$join = ["order_goods b", "order_id", "order_id", "leftJoin"];
$order->setBaseQuery("a", ["order_no", "order_price"], $join)
->field('b.*')
->where($where)
->select();
```

> 其实`setBaseQuery`也支持单表的查询，效果与`queryChain`其实是一样的。

比如：

```php
$order = new OrderDao;
$where = [
    "order_id" => 1,
];
$order->setBaseQuery("a", ["order_no", "order_price"])
->where($where)
->select();
```

上面的第一个参数`$alias`也可以写成空字符串，并不影响单表的查询，第二个参数`$field`也就成了单表的过滤字段。

#### 我们推荐的用法是：

* 单表查询链式操作依旧使用`queryChain`。
* 关联查询链式操作使用`setBaseQuery`。
* 关联查询时，允许不定义别名，无论是主表还是联查表，默认会以表名作为别名处理。

> `BaseModel`提供四种联查方式的快捷方法`selectJoin`，`selectLeftJoin`，`selectRightJoin`，`selectFullJoin`。  
> 注意：`selectFullJoin`联查方式Mysql数据库不支持。

### selectJoin 联查【innerJoin】

> selectJoin \( '查询条件', '筛选字段', '关联规则', '\[ and \]是否or查询', '\[ false \]是否返回model对象' \);

`selectJoin`**如果表中有至少一个匹配，则返回行**

```php
$order = new OrderDao;
$where = [
    "this.order_id" => 1,
];
$join = ["order_goods", "order_id"];
$order->selectJoin($where, [], $join);
```

快捷方法如果不筛选主表字段，默认都会把主表全部字段查询出来，如上面的方式，将会返回主表所有数据，但是不会包含联查表数据。

> 四种联查方式快捷方法的主表别名统一都会默认为字符串“this”，主表字段筛选，请统一使用“this.字段名”

上面的查询将会生成如下SQL：

```php
SELECT `this`.* FROM `topphp_order` `this` INNER JOIN `topphp_order_goods` `order_goods` ON `order_goods`.`order_id`=`this`.`order_id` WHERE `this`.`order_id` = 1
```

筛选字段参数`$fields`默认表示的是筛选**联查表**字段，如：

```php
$order = new OrderDao;
$where = [
    "this.order_id" => 1,
];
$join = ["order_goods b", "order_id"];
$order->selectJoin($where, "b.*", $join);
```

上面将会将主表和联查表所有字段查询出来，生成如下SQL：

```php
SELECT `this`.*,`b`.* FROM `topphp_order` `this` INNER JOIN `topphp_order_goods` `b` ON `b`.`order_id`=`this`.`order_id` WHERE `this`.`order_id` = 1
```

这样查询所有，请注意主表与联查表中是否有重复的字段名，联查表的重复字段值会覆盖主表的，所以我们推荐数组形式进行查询：

```php
$order = new OrderDao;
$where = [
    "this.order_id" => 1,
];
$join = ["order_goods b", "order_id"];
$order->selectJoin($where, ["b.*"], $join);
```

数组形式的字段筛选，生成的联查表数据字段会自动重命名为【表名+下划线+字段名】形式，如上面设置的`order_goods`表别名为`b`，那么查询出来的所有`order_goods`表字段名都会默认加上`b_`的前缀。如果你没有设置别名，前缀将都会变成表名`order_goods_`。

如果你想筛选主表字段，可以按照如下方式筛选：

```php
$order = new OrderDao;
$where = [
    "this.order_id" => 1,
];
$join = ["order_goods b", "order_id"];
$order->selectJoin($where, ["this.order_no", "b.goods_name"], $join);
```

这将生成如下SQL：

```php
SELECT `this`.`order_no`,b.goods_name as b_goods_name FROM `topphp_order` `this` INNER JOIN `topphp_order_goods` `b` ON `b`.`order_id`=`this`.`order_id` WHERE `this`.`order_id` = 1
```

如果是字符串形式的筛选：

```php
$order = new OrderDao;
$where = [
    "this.order_id" => 1,
];
$join = ["order_goods b", "order_id"];
$order->selectJoin($where, "this.order_no,b.goods_name", $join);
```

这将生成如下SQL：

```php
SELECT `this`.`order_no`,`b`.`goods_name` FROM `topphp_order` `this` INNER JOIN `topphp_order_goods` `b` ON `b`.`order_id`=`this`.`order_id` WHERE `this`.`order_id` = 1
```

> 如果你仅需要筛选联查表中不多的几个字段，推荐使用字符串形式的字段筛选，如果你是大批量的查询所有，推荐使用数组形式定义筛选字段。



