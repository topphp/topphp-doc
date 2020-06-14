## 查询

模型查询和数据库查询方法的区别主要在于，模型中的查询的数据在获取的时候会经过获取器的处理，以及更加对象化的获取方式。

> 模型查询除了使用自身的查询方法外，一样可以使用数据库的查询构造器，返回的都是模型对象实例。但如果直接调用查询对象的方法，IDE可能无法完成自动提示。

## 获取单个数据

获取单个数据的方法包括：

```php
// 取出主键为1的数据
$user = User::find(1);
echo $user->name;

// 使用查询构造器查询满足条件的数据
$user = User::where('name', 'thinkphp')->find();
echo $user->name;
```

模型使用`find`方法查询，如果数据不存在返回`Null`，否则返回当前模型的对象实例。如果希望查询数据不存在则返回一个空模型，可以使用。

```php
$user = User::findOrEmpty(1);
```

你可以用`isEmpty`方法来判断当前是否为一个空模型。

```php
$user = User::where('name', 'thinkphp')->findOrEmpty();
if (!$user->isEmpty()) {
    echo $user->name;
}
```

> 如果你是在模型内部获取数据，请不要使用`$this->name`的方式来获取数据，请使用`$this->getAttr('name')`替代。

## 获取多个数据

取出多个数据：

```php
// 根据主键获取多个数据
$list = User::select([1,2,3]);
// 对数据集进行遍历操作
foreach($list as $key=>$user){
    echo $user->name;
}
```

要更多的查询支持，一样可以使用查询构造器：

```php
// 使用查询构造器查询
$list = User::where('status', 1)->limit(3)->order('id', 'asc')->select();
foreach($list as $key=>$user){
    echo $user->name;
}
```

> 查询构造器方式的查询可以支持更多的连贯操作，包括排序、数量限制等。

### 自定义数据集对象

模型的`select`方法返回的是一个包含多个模型实例的数据集对象（默认为`\think\model\Collection`），支持在模型中单独设置查询数据集的返回对象的名称，例如：

```php
<?php
namespace app\index\model;

use think\Model;

class User extends Model
{
	// 设置返回数据集的对象名
	protected $resultSetType = '\app\common\Collection';
}
```

`resultSetType`属性用于设置自定义的数据集使用的类名，该类应当继承系统的`think\model\Collection`类。

## 使用查询构造器

在模型中仍然可以调用数据库的链式操作和查询方法，可以充分利用数据库的查询构造器的优势。

例如：

```php
User::where('id',10)->find();
User::where('status',1)->order('id desc')->select();
User::where('status',1)->limit(10)->select();
```

使用查询构造器直接使用静态方法调用即可，无需先实例化模型。

### 获取某个字段或者某个列的值

```php
// 获取某个用户的积分
User::where('id',10)->value('score');
// 获取某个列的所有值
User::where('status',1)->column('name');
// 以id为索引
User::where('status',1)->column('name','id');
```

> `value`和`column`方法返回的不再是一个模型对象实例，而是纯粹的值或者某个列的数组。

### 动态查询

支持数据库的动态查询方法，例如：

```php
// 根据name字段查询用户
$user = User::getByName('thinkphp');

// 根据email字段查询用户
$user = User::getByEmail('thinkphp@qq.com');
```

### 聚合查询

同样在模型中也可以调用数据库的聚合方法查询，例如：

```php
User::count();
User::where('status','>',0)->count();
User::where('status',1)->avg('score');
User::max('score');
```

注意，如果你的字段不是数字类型，是使用`max`/`min`的时候，需要加上第二个参数。

```php
User::max('name', false);
```

### 数据分批处理

模型也可以支持对返回的数据分批处理，这在处理大量数据的时候非常有用，例如：

```php
User::chunk(100, function ($users) {
    foreach($users as $user){
        // 处理user模型对象
    }
});
```

## 使用游标查询

模型也可以使用数据库的`cursor`方法进行游标查询，返回生成器对象

```php
foreach(User::where('status', 1)->cursor() as $user){
	echo $user->name;
}
```

`user`变量是一个模型对象实例。

## 最佳实践

> 模型查询的最佳实践原则是：在模型外部使用静态方法进行查询，内部使用动态方法查询，包括使用数据库的查询构造器。或者使用`TopPHP`的[`topphp/topphp-generate`](/composer/topphp-generate.md)组件提供的`BaseModel`类的查询章节的相关方法进行数据查询。



