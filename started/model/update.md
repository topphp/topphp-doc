## 更新

和模型新增一样，更新操作同样也会经过修改器、自动完成以及模型事件等处理，并不等同于数据库的数据更新，而且更新方法和新增方法使用的是同一个方法，通常系统会自动判断需要新增还是更新数据。

## 查找并更新

在取出数据后，更改字段内容后使用`save`方法更新数据。**这种方式是最佳的更新方式**。

```php
$user = User::find(1);
$user->name     = 'thinkphp';
$user->email    = 'thinkphp@qq.com';
$user->save();
```

> `save`方法成功返回`true`，并只有当`before_update`事件返回`false`的时候返回`false`，有错误则会抛出异常。

对于复杂的查询条件，也可以使用查询构造器来查询数据并更新

```php
$user = User::where('status',1)
    ->where('name','liuchen')
    ->find();
$user->name     = 'thinkphp';
$user->email    = 'thinkphp@qq.com';
$user->save();
```

`save`方法更新数据，只会更新变化的数据，对于没有变化的数据是不会进行重新更新的。如果你需要强制更新数据，可以使用下面的方法：

```php
$user = User::find(1);
$user->name     = 'thinkphp';
$user->email    = 'thinkphp@qq.com';
$user->force()->save();
```

这样无论你的修改后的数据是否和之前一样都会强制更新该字段的值。

如果要执行SQL函数更新，可以使用下面的方法

```php
$user = User::find(1);
$user->name     = 'thinkphp';
$user->email    = 'thinkphp@qq.com';
$user->score    =  Db::raw('score+1');
$user->save();
```

## 字段过滤

默认情况下会过滤非数据表字段的数据，如果你通过外部提交赋值给模型，并且希望指定某些字段写入，可以使用：

```php
$user = User::find(1);
// post数组中只有name和email字段会写入
$user->allowField(['name', 'email'])->save($_POST);
```

最佳用法是在传入模型数据之前就进行过滤，例如：

```php
$user = User::find(1);
// post数组中只有name和email字段会写入
$data = Request::only(['name','email']);
$user->save($data);
```

## 批量更新数据

可以使用`saveAll`方法批量更新数据，只需要在批量更新的数据中包含主键即可，例如：

```php
$user = new User;
$list = [
    ['id'=>1, 'name'=>'thinkphp', 'email'=>'thinkphp@qq.com'],
    ['id'=>2, 'name'=>'onethink', 'email'=>'onethink@qq.com']
];
$user->saveAll($list);
```

批量更新方法返回的是一个数据集对象。

> 批量更新仅能根据主键值进行更新，其它情况请自行处理。

## 直接更新（静态方法）

使用模型的静态`update`方法更新：

```php
User::update(['name' => 'thinkphp'], ['id' => 1]);
```

> 模型的`update`方法返回模型的对象实例

如果你的第一个参数中包含主键数据，可以无需传入第二个参数（更新条件）

```php
User::update(['name' => 'thinkphp', 'id' => 1]);
```

如果你需要只允许更新指定字段，可以使用

```php
User::update(['name' => 'thinkphp', 'email' => 'thinkphp@qq.com'], ['id' => 1], ['name']);
```

上面的代码只会更新`name`字段的数据。

## 自动识别

我们已经看到，模型的新增和更新方法都是`save`方法，系统有一套默认的规则来识别当前的数据需要更新还是新增。

* 实例化模型后调用`save`方法表示新增；
* 查询数据后调用`save`方法表示更新；

不要在一个模型实例里面做多次更新，会导致部分重复数据不再更新，正确的方式应该是先查询后更新或者使用模型类的`update`方法更新。

> 不要调用`save`方法进行多次数据写入。

## 最佳实践

> 更新的最佳实践原则是：如果需要使用模型事件，那么就先查询后更新，如果不需要使用事件或者不查询直接更新，直接使用静态的`Update`方法进行条件更新，如非必要，尽量不要使用批量更新，或者使用`TopPHP`的[`topphp/topphp-generate`](/composer/topphp-generate.md)组件提供的`BaseModel`类的`edit`方法进行编辑数据，`updateField`方法进行指定条件更新，`updateAll`方法进行批量更新。

`edit`方法主要针对数据表单的更新操作，例如我们有一个需要对于用户信息编辑的页面，我们可以直接调用编辑的方法进行展示数据，编辑方法在仅传入主键`id`时会根据主键自动查询数据并返回：

```php
$data = [
    'id' = 1;
];
$user = new UserDao;
$user->edit($data);
```

