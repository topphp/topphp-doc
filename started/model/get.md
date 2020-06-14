## 获取器

获取器的作用是对模型实例的（原始）数据做出自动处理。一个获取器对应模型的一个特殊方法（该方法必须为`public`类型），方法命名规范为：

> ### get`FieldName`Attr

`FieldName`为数据表字段的驼峰转换，定义了获取器之后会在下列情况自动触发：

* 模型的数据对象取值操作（`$model->field_name`）；
* 模型的序列化输出操作（`$model->toArray()`及`toJson()`）；
* 显式调用`getAttr`方法（`$this->getAttr('field_name')`）；

获取器的场景包括：

* 时间日期字段的格式化输出；
* 集合或枚举类型的输出；
* 数字状态字段的输出；
* 组合字段的输出；

例如，我们需要对状态值进行转换，可以使用：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    public function getStatusAttr($value)
    {
        $status = [-1=>'删除',0=>'禁用',1=>'正常',2=>'待审核'];
        return $status[$value];
    }
}
```

数据表的字段会自动转换为驼峰法，一般`status`字段的值采用数值类型，我们可以通过获取器定义，自动转换为字符串描述。

```php
$user = User::find(1);
echo $user->status; // 例如输出“正常”
```

获取器还可以定义数据表中不存在的字段，例如：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    public function getStatusTextAttr($value,$data)
    {
        $status = [-1=>'删除',0=>'禁用',1=>'正常',2=>'待审核'];
        return $status[$data['status']];
    }
}
```

> 获取器方法的第二个参数传入的是当前的所有数据数组。

我们就可以直接使用status\_text字段的值了，例如：

```php
$user = User::find(1);
echo $user->status_text; // 例如输出“正常”
```

## 获取原始数据

如果你定义了获取器的情况下，希望获取数据表中的原始数据，可以使用：

```php
$user = User::find(1);
// 通过获取器获取字段
echo $user->status;
// 获取原始字段数据
echo $user->getData('status');
// 获取全部原始数据
dump($user->getData());
```

## 动态获取器

可以支持对模型使用动态获取器，无需在模型类中定义获取器方法。

```php
User::withAttr('name', function($value, $data) {
	return strtolower($value);
})->select();
```

> `withAttr`方法支持多次调用，定义多个字段的获取器。另外注意，`withAttr`方法之后不能再使用模型的查询方法，必须使用Db类的查询方法。

> 如果同时还在模型里面定义了相同字段的获取器，则动态获取器优先，也就是可以临时覆盖定义某个字段的获取器。

支持对关联模型的字段使用动态获取器，例如：

```php
User::with('profile')->withAttr('profile.name', function($value, $data) {
	return strtolower($value);
})->select();
```

> 注意：对于`MorphTo`关联不支持使用动态获取器。

并且支持对`JSON`字段使用获取器，例如在模型中定义了`JSON`字段的话：

```php
<?php
namespace app\index\model;

use think\Model;

class User extends Model
{
	// 设置json类型字段
	protected $json = ['info'];
}
```

可以使用下面的代码定义JSON字段的获取器。

```php
User::withAttr('info.name', function($value, $data) {
	return strtolower($value);
})->select();
```

可以在查询之后使用`withAttr`方法，例如：

```php
User::select()->withAttr('name', function($value, $data) {
	return strtolower($value);
});
```



