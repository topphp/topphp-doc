## 新增

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

如果你想批量新增，可以使用`addAll`方法：

```php
$user = new UserDao;
$list = [
    ['name'=>'thinkphp','email'=>'thinkphp@qq.com'],
    ['name'=>'onethink','email'=>'onethink@qq.com']
];
$user->addAll($list);
```