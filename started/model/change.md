## 修改器

和获取器相反，修改器的主要作用是对模型设置的数据对象值进行处理。

修改器方法的命名规范为：

> ### set`FieldName`Attr

修改器的使用场景和读取器类似：

* 时间日期字段的转换写入；
* 集合或枚举类型的写入；
* 数字状态字段的写入；
* 某个字段涉及其它字段的条件或者组合写入；

定义了修改器之后会在下列情况下触发：

* 模型对象赋值；
* 调用模型的`data`方法，并且第二个参数传入`true`；
* 调用模型的`save`方法，并且传入数据；
* 显式调用模型的`setAttr`方法；

例如：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    public function setNameAttr($value)
    {
        return strtolower($value);
    }
}
```

如下代码实际保存到数据库中的时候会转为小写

```php
$user = new User();
$user->name = 'THINKPHP';
$user->save();
echo $user->name; // thinkphp
```

也可以进行序列化字段的组装：

```php
namespace app\model;

use think\Model;

class User extends Model 
{
    public function setSerializeAttr($value,$data)
    {
        return serialize($data);
    }
}
```

> 修改器方法的第二个参数会自动传入当前的所有数据数组。

如果你需要在修改器中修改其它数据，可以使用

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    public function setTestFieldAttr($value, $data)
    {
        $this->set('other_field', $data['some_field']);
    }
}
```

上面的例子，在`test_field`字段的修改器中修改了`other_field`字段数据，并且没有返回值（表示不对`test_field`字段做任何修改）。

## 批量修改

除了赋值的方式可以触发修改器外，还可以用下面的方法批量触发修改器：

```php
$user = new User();
$data['name'] = 'THINKPHP';
$data['email'] = 'thinkphp@qq.com';
$user->data($data, true);
$user->save();
echo $user->name; // thinkphp
```

如果为`name`和`email`字段都定义了修改器的话，都会进行处理。

或者直接使用save方法触发，例如：

```php
$user = new User();
$data['name'] = 'THINKPHP';
$data['email'] = 'thinkphp@qq.com';
$user->save($data);
echo $user->name; // thinkphp
```

> 修改器方法仅对模型的写入方法有效，调用数据库的写入方法写入无效，例如下面的方式修改器无效。

```php
$user = new User();
$data['name'] = 'THINKPHP';
$data['email'] = 'thinkphp@qq.com';
$user->insert($data);
```



