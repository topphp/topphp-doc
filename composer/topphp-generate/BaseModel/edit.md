## 修改和编辑

### 编辑数据

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
