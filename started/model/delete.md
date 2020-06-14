## 删除

模型的删除和数据库的删除方法区别在于，模型的删除会包含模型的事件处理。

## 删除当前模型

删除模型数据，可以在查询后调用`delete`方法。

```php
$user = User::find(1);
$user->delete();
```

> `delete`方法返回布尔值

## 根据主键删除

或者直接调用静态方法（根据主键删除）

```php
User::destroy(1);
// 支持批量删除多个数据
User::destroy([1,2,3]);
```

> 当`destroy`方法传入空值（包括空字符串和空数组）的时候不会做任何的数据删除操作，但传入0则是有效的

## 条件删除

还支持使用闭包删除，例如：

```php
User::destroy(function($query){
    $query->where('id','>',10);
});
```

或者通过数据库类的查询条件删除

```php
User::where('id','>',10)->delete();
```

> 直接调用数据库的`delete`方法的话无法调用模型事件。

## 最佳实践

> 删除的最佳实践原则是：如果删除当前模型数据，用`delete`方法，如果需要直接删除数据，使用`destroy`静态方法。或者使用`TopPHP`的[`topphp/topphp-generate`](/composer/topphp-generate.md)组件提供的`BaseModel`类的`remove`方法进行数据删除。

如果需要删除一条数据，只需要：

```php
$user = new UserDao;
$user->remove(1);
```

按照指定条件删除：

```php
$user = new UserDao;
$ids = [4, 5, 6];
$where = ["id", "in", $ids];
$user->remove($where);
```

> `remove`方法默认软删除，条件支持传入主键`id`、关联数组、表达式形式的索引数组（多条件的可以是二维数组）、闭包。

如果你需要真实删除，也很简单，只需要传入第二个参数`$isTrueDel`为`true`即可：

```php
$user = new UserDao;
$where = [
    "name" => "topphp"
];
$user->remove($where, true);
```

> `remove`方法真实删除，将直接删除满足条件的数据，无论该数据是否已软删除。

可以通过第三个参数来控制删除条件是否是`or`条件：

```php
$user = new UserDao;
$ids  = [2, 7, 10];
$where = [
    ["id", "in", $ids],
    ["id", ">", 32]
];
$user->remove($where, false, "or");
```

或者闭包传入`or`条件：

```php
$user = new UserDao;
$ids  = [2, 7, 10];
$where = function ($query) use ($ids) {
    $query->where("id", "in", $ids);
    $query->whereOr("id", ">", 32);
};
$user->remove($where);
```

上面的两种方式都可以达到同一种效果。

> `remove`方法返回的是删除成功的记录数。



