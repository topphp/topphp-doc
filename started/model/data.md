## 数据集

> 模型的`select`查询方法返回数据集对象`think\model\Collection`，该对象继承自`think\Collection`，因此具有数据库的[数据集类](https://www.kancloud.cn/manual/thinkphp6_0/1037576)的所有方法，而且还提供了额外的模型操作方法。

基本用法和数组一样，例如可以遍历和直接获取某个元素。

```php
// 模型查询返回数据集对象
$list = User::where('id', '>', 0)->select();
// 获取数据集的数量
echo count($list);
// 直接获取其中的某个元素
dump($list[0]);
// 遍历数据集对象
foreach ($list as $user) {
    dump($user);
}
// 删除某个元素
unset($list[0]);
```

需要注意的是，如果要判断数据集是否为空，不能直接使用`empty`判断，而必须使用数据集对象的`isEmpty`方法判断，例如：

```php
$users = User::select();
if($users->isEmpty()){
    echo '数据集为空';
}
```

你可以使用模型的`hidden`/`visible`/`append`/`withAttr`方法进行数据集的输出处理，例如：

```php
// 模型查询返回数据集对象
$list = User::where('id', '>', 0)->select();
// 对输出字段进行处理
$list->hidden(['password']) 
        ->append(['status_text'])
        ->withAttr('name', function($value, $data) {
        return strtolower($value);
    });
dump($list);
```

如果需要对数据集的结果进行筛选，可以使用：

```php
// 模型查询返回数据集对象
$list = User::where('id', '>', 0)->select()
    ->where('name', 'think')
    ->where('score', '>', 80);
dump($list);
```

支持`whereLike`/`whereIn`/`whereBetween`等快捷方法。

```php
// 模型查询返回数据集对象
$list = User::where('id', '>', 0)->select()
    ->whereLike('name', 'think%')
    ->whereBetween('score', [80,100]);
dump($list);
```

支持数据集的`order`排序操作。

```php
// 模型查询返回数据集对象
$list = User::where('id', '>', 0)->select()
    ->where('name', 'think')
    ->where('score', '>', 80)
    ->order('create_time','desc');
dump($list);
```

支持数据集的`diff/intersect`操作。

```php
// 模型查询返回数据集对象
$list1 = User::where('status', 1)->field('id,name')->select();
$list2 = User::where('name', 'like', 'think')->field('id,name')->select();
// 计算差集
dump($list1->diff($list2));
// 计算交集
dump($list1->intersect($list2));
```

## 批量删除和更新数据

支持对数据集的数据进行批量删除和更新操作，例如：

```php
$list = User::where('status', 1)->select();
$list->update(['name' => 'php']);

$list = User::where('status', 1)->select();
$list->delete();
```



