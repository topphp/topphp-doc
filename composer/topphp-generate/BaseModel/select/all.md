## 查询所有

### selectAll 查询

> selectAll \( '查询条件', '排除字段', '\[ and \]是否or查询', '\[ false \]是否返回model对象' \);

```php
$user = new UserDao;
$where = [
    'name' => 'topphp'
];
$user->selectAll($where);
```

如果你需要排除某个字段，除了设置永久隐藏`$hidden`属性外还可以使用`selectAll`的第二个参数进行排除字段处理：

```php
$user = new UserDao;
$where = [
    'name' => 'topphp'
];
$user->selectAll($where, "password");
```

> `selectAll`的第二个参数`$withoutField`支持数组或字符串`field1,field2`形式。

**注意：使用了`$withoutField`参数进行排除字段，那么查询出来的数据，使用`getModelData`就也获取不到被排除的字段信息了。**