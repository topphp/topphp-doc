## 新增

> add( '数据数组', '\[ bool \]是否返回当前模型' );

通常我们新增一条数据只需要构造存储数据数组直接传给`add`方法即可：

```php
$data = [
    'name' => 'thinkphp',
    'email' => 'thinkphp@qq.com'
];
$user = new UserDao;
$user->add($data);
```
> `add`方法不同于`ThinkPHP`的`save`方法，它的新增返回的是包含自增主键值的当前新增数据信息，省去了开发者再次获取自增主键并赋值的步骤，默认会过滤数据库不存在的字段。

如果你想新增成功以后还要对此模型进行其他的操作，可以传入第二个参数`$isModel`值为`true`，返回模型对象进行链式操作。

如：新增成功以后你想获取当前新增的所有数据，可以像如下这样在`UserDao`模型中使用：

```php
$data = [
    'name' => 'thinkphp',
    'email' => 'thinkphp@qq.com'
];
$idName = $this->getPk();
$this->add($data, true)->where($idName, $this->$idName)->find();
```

上面的调用方式将会返回当前新增的所有数据，与第一种不同的是返回的信息不会包含数据库不存在的字段了。事实上第一种就是将`$data`数据加入自增的主键返回给你，而第二种是新增后再次查询当前模型，不过这样将会额外增加一次查询的操作，如没有必要不推荐这样使用。

### 批量新增

> addAll( '数据二维数组', '\[ bool \]是否返回当前模型' );

如果你想批量新增，可以使用`addAll`方法：

```php
$user = new UserDao;
$list = [
    ['name'=>'thinkphp','email'=>'thinkphp@qq.com'],
    ['name'=>'onethink','email'=>'onethink@qq.com']
];
$user->addAll($list);
```

> `addAll`方法返回的是包含自增主键值的当前新增数据信息。同样`addAll`支持第二个参数`$isModel`值为`true`，返回模型对象进行链式操作的方式。

```php
$user = new UserDao;
$list = [
    ['name'=>'thinkphp','email'=>'thinkphp@qq.com'],
    ['name'=>'onethink','email'=>'onethink@qq.com']
];
$pageConfig = $user->getPaginateConfig(10);
$user->addAll($list, true)->paginate($pageConfig)->toArray();
```

上面的方法将返回包含了新增数据的所有数据集的分页数据（每页显示10条记录），这样即可达到新增与返回查询分页一气呵成。关于分页的说明可以查看[`分页及其他`](/composer/topphp-generate/BaseModel/page.md)板块。

### 大数据量批量新增

> addAll( '数据二维数组', '\[ int \]分批插入限制条数', '\[ bool \]是否自动时间戳' );

如果你的数据量特别大，直接批量新增可能会导致数据库卡死或者是超时，使用`addLimitAll`方法分批次插入数据是个更好的选择，`addLimitAll`一般应用于批量数据超千条的场景：

```php
$user = new UserDao;
$list = [
    ['name'=>'thinkphp','email'=>'thinkphp@qq.com'],
    ['name'=>'onethink','email'=>'onethink@qq.com'],
    ...
];
$user->addLimitAll($list);
```

> `addLimitAll`方法返回的是插入成功的记录数，支持传入第二个参数`$limit`来控制每次执行插入的数量限制，默认`100`。

```php
$user = new UserDao;
$list = [
    ['name'=>'thinkphp','email'=>'thinkphp@qq.com'],
    ['name'=>'onethink','email'=>'onethink@qq.com'],
    ...
];
$user->addLimitAll($list, 50);
```

> `addLimitAll`方法第三个参数`$autoTime`用来控制是否自动时间戳，仅数据表存在 `create_time` 或 `update_time` 字段有效，默认为`true`开启状态。

如果你的数据表不存在 `create_time` 或 `update_time` 字段也不会影响插入。当你的数据是从旧的数据导入进来时，你又想保留原有的 `create_time` 或 `update_time` 字段数据，可能就需要将`$autoTime`参数设为`false`了。

```php
$user = new UserDao;
$list = [
    ['name'=>'thinkphp','email'=>'thinkphp@qq.com','create_time'=>'1999-01-01 13:36:25'],
    ['name'=>'onethink','email'=>'onethink@qq.com','create_time'=>'2000-10-05 10:50:30'],
    ...
];
$user->addLimitAll($list, 50, false);
```
