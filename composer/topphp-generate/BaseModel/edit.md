## 修改和编辑

### 编辑数据

> edit ( '数据数组', '\[ string \]软删除过滤判断', '\[ bool \]是否返回当前模型' );

`edit`方法主要针对数据表单的更新操作，例如我们有一个需要对于用户信息编辑的页面，我们可以直接调用编辑的方法进行展示数据，编辑方法在仅传入主键id时会根据主键自动查询数据并返回：

```php
$user = new UserDao;
$user->edit(['id' => 1]);
```
当我们修改信息，再次提交时，添加了修改的字段信息，编辑方法会实时更新数据，并返回最新的数据：

```php
$data = [
    'id' => 1,
    'name' => 'thinkphp'
];
$user = new UserDao;
$user->edit($data);
```
> 需要注意的是`edit`方法默认会过滤软删除数据，如果你的数据已经被软删除，将不会更新成功，也不会查询出来。

如果你需要编辑软删除的数据，我们提供第二个参数`$queryType`来过滤软删除数据：

```php
$data = [
    'id' => 1,
    'name' => 'thinkphp'
];
$user = new UserDao;
$user->edit($data, 'withSoft');
// $user->edit($data, MethodEnum::WITH_SOFT);// 或者使用枚举类
```
枚举类对应的软删除类型为：

| queryType | 枚举常量 | 说明 |
| :--- | :--- | :--- |
| excludeSoft | MethodEnum::EXCLUDE_SOFT | 排除软删除 |
| withSoft | MethodEnum::WITH_SOFT | 包含软删除 |
| onlySoft | MethodEnum::ONLY_SOFT | 仅查询软删除 |

有的时候我们需要编辑完返回列表数据，常规情况是一个编辑接口，一个列表接口，前端编辑完，再次调用列表接口，这样会产生两次调用请求，`edit`方法的第三个参数`$isModel`同样支持直接返回模型对象，供我们链式调用：

```php
$data = [
    'id' => 1,
    'name' => 'thinkphp'
];
$user = new UserDao;
$pageConfig = $user->getPaginateConfig(10);
$user->edit($data, MethodEnum::EXCLUDE_SOFT, true)->paginate($pageConfig)->toArray();
```
上面的方式将直接返回编辑完的数据列表最新分页数据，前端可以直接根据返回值进行渲染，不需要再次调用查询列表接口了。

### 更新指定字段值

> updateField ( '查询条件', '字段名/字段键值对数组', '字段值/不传' );

如果你需要仅更新指定的字段，或者是根据指定的条件更新，你可以使用`updateField`方法，如我们将`name`为`thinkphp`的记录字段`name`更新为`topphp`：

```php
$where = [
    'name' => 'thinkphp'
];
$user = new UserDao;
$user->updateField($where, "name", "topphp");
```

> `where`条件支持传入主键`id`、关联数组、表达式形式的索引数组（多条件的可以是二维数组）、闭包。

多个字段可以使用数组形式：

```php
$data = [
    'name'  => 'topphp',
    'email' => 'topphp@domain.com'
];
$where = ['name', '=', 'thinkphp'];
$user = new UserDao;
$user->updateField($where, $data);
```

还可以直接通过主键进行更新：

```php
$user = new UserDao;
$user->updateField(1, "name", "topphp");
```

> `updateField`方法返回的是`bool`值，更新成功返回`true`，失败返回`false`。  
> 注意：`updateField`方法不会自动过滤软删除数据，如果你有过滤软删除这种特殊的需求，可以自行在`where`条件中加入筛选判断，如`$where = [["id", ">", 10], ["delete_time", "=", null]]`;

### 多条件批量更新

> updateAll ( '查询条件/包含主键的字段键值对二维数组', '字段键值对数组/不传', '\[ string \]是否OR查询/不传默认AND' );

`updateField`其实是支持按照条件批量更新的，但是如果你是更复杂的多条件批量更新或者希望通过主键进行批量更新可以使用`updateAll`方法。

例如：我们需要把一个角色`id`和角色`name`更新到一批管理员用户身上：

```php
$admin = new AdminDao;
$data = [
    "role_id" => 1,
    "role_name" => "财务"
];
$ids  = [1, 6, 10];
$where = function ($query) use ($ids) {
    $query->where("id", "in", $ids);
    $query->whereOr("id", ">", 32);
};
$admin->updateAll($where, $data);
```

上面的调用将会生成如下SQL:

```php
UPDATE `topphp_admin`  SET `role_id` = '1' , `role_name` = '财务' , `update_time` = '2020-05-31 18:20:37'  WHERE  ( `id` IN (1,6,10) OR `id` > 32 )
```

上面的写法是一个典型的闭包查询条件，利用闭包查询，我们可以构造很多复杂的查询条件。`BaseModel`的很多方法的`where`条件参数几乎都支持这种闭包的写法。

`updateAll`方法返回的是更新成功的记录数。上面的用法其实使用`updateField`也可以做到，区别就是两个方法的返回值不同。但是如果你想通过主键`id`进行批量更新，那么只有`updateAll`才能做到了：

```php
$admin = new AdminDao;
$data  = [
    [
        "id"        => 11,
        "role_id"   => 1,
        "role_name" => "财务"
    ],
    [
        "id"        => 20,
        "role_id"   => 3,
        "role_name" => "总经理"
    ],
];
$admin->updateAll($data);
```

我们可以在`updateAll`的第一个参数传入包含主键`id`的二维更新数组，`updateAll`会将所有满足条件的数据进行对应的批量更新。

`updateAll`同样也支持像`updateField`那样通过主键进行多字段修改：

```php
$admin = new AdminDao;
$data = [
    "role_id" => 2,
    "role_name" => "行政"
];
$admin->updateAll(1, $data);
```

> `updateAll`与`updateField`还有一个不同的地方就是`updateAll`会自动过滤掉数据表不存在的字段，而`updateField`则会报出`fields not exists:[xxx]`的异常。

`updateAll`的第三个参数`$isOr`表示`where`条件是否是`OR`查询，默认`string`类型的`and`，构造时相当于使用`ThinkPHP`的链式`$model->where()`，如果你传入字符串`or`，构造时相当于使用`ThinkPHP`的链式`$model->whereOr()`。

```php
$admin = new AdminDao;
$data = [
    "role_id" => 1,
    "role_name" => "财务"
];
$ids  = [1, 6, 10];
$where = [
    ["id", "in", $ids],
    ["id", ">", 32]
];
$admin->updateAll($where, $data, "or");
```

上面的写法其实与第一个闭包形式的结果是一样的。

#### 我们推荐的用法是：

* 如果你是需要编辑一条数据表单，使用`edit`
* 如果你不需要获取更新成功的记录数或者仅需要更新单一字段，使用`updateField`
* 如果需要获取更新成功的记录数，或者需要通过主键进行批量更新，使用`updateAll`