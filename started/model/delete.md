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



