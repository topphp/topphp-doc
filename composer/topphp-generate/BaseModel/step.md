## 字段自增和自减

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