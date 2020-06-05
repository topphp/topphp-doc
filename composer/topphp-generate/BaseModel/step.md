## 字段自增和自减

### 自增

> fieldInc \( '查询条件', '字段名', '\[ 1 \]步进值' \);

如果我们需要对某个字段进行自增操作，可以使用`fieldInc`方法：

```php
$user = new UserDao;
$where = [
    "name" => "zhangsan"
];
$user->fieldInc($where, "score");
```

> `fieldInc`的返回值为`bool`，`true`为成功，`false`为失败，第三个参数`$step`为步进值，默认每次自增`1`。自增支持`float`浮点型步进。

```php
$user = new UserDao;
$where = [
    "name" => "zhangsan"
];
$user->fieldInc($where, "score", 0.5);
```

需要注意的是，如果你使用`float`型的自增或者自减，对应的数据表字段也应该是支持浮点型的字段，如果是`int`型的字段，传入了`float`型的步进值，将会对步进值四舍五入成`int`型写入。

### 自减

> fieldDec \( '查询条件', '字段名', '\[ 1 \]步进值' \);

如果我们需要对某个字段进行自减操作，可以使用`fieldDec`方法：

```php
$user = new UserDao;
$user->fieldDec(1, "score");
```

> `fieldDec`用法与`fieldInc`一致，并且查询条件都支持传入主键`id`、关联数组、表达式形式的索引数组（多条件的可以是二维数组）、闭包。

### 多字段自增/自减

> fieldStep \( '查询条件', '字段表达式二维数组' \);

`fieldStep`的第二个参数规则为：\[ \['字段名1','inc','步进值1'\], \['字段名2','dec','步进值2'\] \]

如果你有多字段自增自减的需求可以使用`fieldStep`方法，比如商品表的购买数量自增，库存自减：

```php
$goods = new GoodsDao;
$where = ["goods_id", "in", [1,3,6]];
$fields = [
    ["buy_num", "inc", 1],
    ["stock", "dec", 1]
];
$goods->fieldStep($where, $fields);
```

`fieldStep`方法的`$fields`也支持一围数组的表达式形式的自增或自减：

```php
$goods = new GoodsDao;
$where = ["goods_id", "in", [1,3,6]];
$fields = ["stock", "inc", 100];
$goods->fieldStep($where, $fields);
```