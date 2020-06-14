## 关联统计

有些时候，并不需要获取关联数据，而只是希望获取关联数据的统计，这个时候可以使用`withCount`方法进行指定关联的统计。

```php
$list = User::withCount('cards')->select([1,2,3]);
foreach($list as $user){
    // 获取用户关联的card关联统计
    echo $user->cards_count;
}
```

> 你必须给User模型定义一个名称是`cards`的关联方法。

关联统计功能会在模型的对象属性中自动添加一个以“关联方法名+`_count`”为名称的动态属性来保存相关的关联统计数据。

可以通过数组的方式同时查询多个统计字段。

```php
$list = User::withCount(['cards', 'phone'])->select([1,2,3]);
foreach($list as $user){
    // 获取用户关联关联统计
    echo $user->cards_count;
    echo $user->phone_count;
}
```

支持给关联统计指定统计属性名，例如：

```php
$list = User::withCount(['cards' => 'card_count'])->select([1,2,3]);
foreach($list as $user){
    // 获取用户关联的card关联统计
    echo $user->card_count;
}
```

> 关联统计暂不支持多态关联

如果需要对关联统计进行条件过滤，可以使用闭包方式。

```php
$list = User::withCount(['cards' => function($query) {
    $query->where('status',1);
}])->select([1,2,3]);

foreach($list as $user){
    // 获取用户关联的card关联统计
    echo $user->cards_count;
}
```

使用闭包的方式，如果需要自定义统计字段名称，可以使用

```php
$list = User::withCount(['cards' => function($query, &$alias) {
    $query->where('status',1);
    $alias = 'card_count';
}])->select([1,2,3]);

foreach($list as $user){
    // 获取用户关联的card关联统计
    echo $user->card_count;
}
```

和`withCount`类似的方法，还包括：

| 关联统计方法 | 描述 |
| :--- | :--- |
| `withSum` | 关联SUM统计 |
| `withMax` | 关联Max统计 |
| `withMin` | 关联Min统计 |
| `withAvg` | 关联Avg统计 |

除了`withCount`之外的统计方法需要在第二个字段传入统计字段名，用法如下：

```php
$list = User::withSum('cards', 'total')->select([1,2,3]);

foreach($list as $user){
    // 获取用户关联的card关联余额统计
    echo $user->cards_sum;
}
```

同样，也可以指定统计字段名

```php
$list = User::withSum(['cards' => 'card_total'], 'total')->select([1,2,3]);

foreach($list as $user){
    // 获取用户关联的card关联余额统计
    echo $user->card_total;
}
```

所有的关联统计方法可以多次调用，每次查询不同的关联统计数据。

```php
$list = User::withSum('cards', 'total')
    ->withSum('score', 'score') 
    ->select([1,2,3]);

foreach($list as $user){
    // 获取用户关联的card关联余额统计
    echo $user->card_total;
}
```



