## 只读字段

只读字段用来保护某些特殊的字段值不被更改，这个字段的值一旦写入，就无法更改。 要使用只读字段的功能，我们只需要在模型中定义`readonly`属性：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    protected $readonly = ['name', 'email'];
}
```

例如，上面定义了当前模型的`name`和`email`字段为只读字段，不允许被更改。也就是说当执行更新方法之前会自动过滤掉只读字段的值，避免更新到数据库。

下面举个例子说明下：

```php
$user = User::find(5);
 // 更改某些字段的值
$user->name = 'TOPThink';
$user->email = 'Topthink@gmail.com';
$user->address = '上海静安区';
 // 保存更改后的用户数据
$user->save();
```

事实上，由于我们对`name`和`email`字段设置了只读，因此只有`address`字段的值被更新了，而`name`和`email`的值仍然还是更新之前的值。

支持动态设置只读字段，例如：

```php
$user = User::find(5);
 // 更改某些字段的值
$user->name = 'TOPThink';
$user->email = 'Topthink@gmail.com';
$user->address = '上海静安区';
 // 保存更改后的用户数据
$user->readonly(['name','email'])->save();
```

> 只读字段仅针对模型的更新方法，如果使用数据库的更新方法则无效，例如下面的方式无效。

```php
$user = new User;
 // 要更改字段值
$data['name'] = 'TOPThink';
$data['email'] = 'Topthink@gmail.com';
$data['address'] = '上海静安区';
 // 保存更改后的用户数据
$user->where('id', 5)->update($data);
```



