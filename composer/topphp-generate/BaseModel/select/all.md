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

> `selectAll`的第二个参数`$withoutField`支持数组或字符串`field1,field2`形式。排除字段和隐藏字段的过滤方式不一样。

**注意：使用了`$withoutField`参数进行排除字段，那么查询出来的数据，使用`getModelData`就也获取不到被排除的字段信息了。**

之所以`selectAll`方法的第二个参数设置的是`排除字段`而不是`筛选字段`，是因为大多数情况下，我们都是查询数据表所有的数据，可能某些文本字段的值查询非常耗内存，查询时又不需要这样的数据，所以需要做排除处理。

设置第四个参数`$isModel`的目的是，往往我们的查询可能不仅限于直接查询出来，可能还需要其他的链式操作，所以提供直接返回`model`对象的方式，如我们还需要排序和一些更复杂的链式操作：

```php
$user = new UserDao;
$ids = [1, 3, 9];
$where = [
    ["id", "in", $ids],
    ["id", ">", 10]
];
$user->selectAll($where, [], "or", true)
    ->order("id", "desc")->select()
    ->each(function ($item) {
         if ($item['id'] == 9) {
             $item['admin_name'] = "topphp";
         }
    })->toArray();
```