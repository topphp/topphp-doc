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

通常你使用`TopPHP`的`BaseModel`类进行链式操作时都要使用`queryChain`进行链式操作的开始，如果你想直接使用`ThinkPHP`的链式操作联查方式也是可以的：

```php
$order = new OrderDao;
$where = [
    "id" => 1,
];
$order->queryChain($where)
->alias('a')
->leftJoin('order_goods b','a.order_id = b.order_id')
->select();
```


