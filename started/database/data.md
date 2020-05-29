## 数据集

数据库的查询结果默认返回数据集对象。

```php
// 获取数据集
$users = Db::name('user')->select();
// 遍历数据集
foreach($users as $user){
    echo $user['name'];
    echo $user['id'];
}
```

返回的数据集对象是`think\Collection`，提供了和数组无差别用法，并且另外封装了一些额外的方法。

> 在模型中进行数据集查询，全部返回数据集对象，但使用的是`think\model\Collection`类（继承`think\Collection`），但用法是一致的。

可以直接使用数组的方式操作数据集对象，例如：

```php
// 获取数据集
$users = Db::name('user')->select();
// 直接操作第一个元素
$item  = $users[0];
// 获取数据集记录数
$count = count($users);
// 遍历数据集
foreach($users as $user){
    echo $user['name'];
    echo $user['id'];
}
```

需要注意的是，如果要判断数据集是否为空，不能直接使用`empty`判断，而必须使用数据集对象的`isEmpty`方法判断，例如：

```php
$users = Db::name('user')->select();
if($users->isEmpty()){
    echo '数据集为空';
}
```

`Collection`类包含了下列主要方法：

| 方法 | 描述 |
| :--- | :--- |
| isEmpty | 是否为空 |
| toArray | 转换为数组 |
| all | 所有数据 |
| merge | 合并其它数据 |
| diff | 比较数组，返回差集 |
| flip | 交换数据中的键和值 |
| intersect | 比较数组，返回交集 |
| keys | 返回数据中的所有键名 |
| pop | 删除数据中的最后一个元素 |
| shift | 删除数据中的第一个元素 |
| unshift | 在数据开头插入一个元素 |
| push | 在结尾插入一个元素 |
| reduce | 通过使用用户自定义函数，以字符串返回数组 |
| reverse | 数据倒序重排 |
| chunk | 数据分隔为多个数据块 |
| each | 给数据的每个元素执行回调 |
| filter | 用回调函数过滤数据中的元素 |
| column | 返回数据中的指定列 |
| sort | 对数据排序 |
| order | 指定字段排序 |
| shuffle | 将数据打乱 |
| slice | 截取数据中的一部分 |
| map | 用回调函数处理数组中的元素 |
| where | 根据字段条件过滤数组中的元素 |
| whereLike | Like查询过滤元素 |
| whereNotLike | Not Like过滤元素 |
| whereIn | IN查询过滤数组中的元素 |
| whereNotIn | Not IN查询过滤数组中的元素 |
| whereBetween | Between查询过滤数组中的元素 |
| whereNotBetween | Not Between查询过滤数组中的元素 |



