## 关联查询

### 链式操作

> setBaseQuery \( '当前表别名', '当前表字段名数组', '关联规则', '\[ join \]关联方式' \);

参数说明：

```
1、$alias 当前表别名，如 'order' 表，别名 'o'。
2、$field 当前表字段数组，如 'order' 表的字段 [field1,field2...]，如果需要查询所有字段直接传 '空数组' 或者是 通配符字符串 '*' 。
3、$join 关联规则，需要连接的表（以下称"联查表"），参数为数组形式，结构如下：

    ['联查表 (别名)', '联查表外键字段', ('主表主键字段')]

    上面的括号表示可选，如果你的主键与外键的字段名一致的话，可以省略第三个参数。
    以上第三个【主表主键字段】参数不传，默认使用【联查表外键字段】进行联查。
    
    如果是多张表联查，可以使用二维数组形式，如：
    [['order_goods og', 'order_id', 'id'],...]
    
4、$type 关联方式，默认'join'方式，可选'leftJoin'，'rightJoin'，'fullJoin'。
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

**需要注意的是`setBaseQuery`方法默认都是针对主表的数据查询，所以上面的`*`筛选出来的都是主表（即当前表的数据），如果你需要获取子表的数据还需要链式操作通过`field`方法来筛选：**

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
$order->setBaseQuery("a", ["order_no"], $join, "leftJoin")
->field('b.*')
->where($where)
->select();
```
