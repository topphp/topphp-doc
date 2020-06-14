## 软删除

在实际项目中，对数据频繁使用删除操作会导致性能问题，软删除的作用就是把数据加上删除标记，而不是真正的删除，同时也便于需要的时候进行数据的恢复。

要使用软删除功能，需要引入`SoftDelete`trait，例如`User`模型按照下面的定义就可以使用软删除功能：

```php
<?php
namespace app\model;

use think\Model;
use think\model\concern\SoftDelete;

class User extends Model
{
    use SoftDelete;
    protected $deleteTime = 'delete_time';
}
```

`deleteTime`属性用于定义你的软删除标记字段，`ThinkPHP`的软删除功能使用时间戳类型（数据表默认值为`Null`），用于记录数据的删除时间。

可以支持`defaultSoftDelete`属性来定义软删除字段的默认值，在此之前的版本，软删除字段的默认值必须为`null`。

```php
<?php
namespace app\model;

use think\Model;
use think\model\concern\SoftDelete;

class User extends Model
{
    use SoftDelete;
    protected $deleteTime = 'delete_time';
    protected $defaultSoftDelete = 0;
}
```

> 可以用类型转换指定软删除字段的类型，建议数据表的所有时间字段统一一种类型。

定义好模型后，我们就可以使用：

```php
// 软删除
User::destroy(1);
// 真实删除
User::destroy(1,true);

$user = User::find(1);
// 软删除
$user->delete();
// 真实删除
$user->force()->delete();
```

默认情况下查询的数据不包含软删除数据，如果需要包含软删除的数据，可以使用下面的方式查询：

```php
User::withTrashed()->find();
User::withTrashed()->select();
```

如果仅仅需要查询软删除的数据，可以使用：

```php
User::onlyTrashed()->find();
User::onlyTrashed()->select();
```

恢复被软删除的数据

```php
$user = User::onlyTrashed()->find(1);
$user->restore();
```

> 软删除的删除操作仅对模型的删除方法有效，如果直接使用数据库的删除方法则无效，例如下面的方式无效。

```php
$user = new User;
$user->where('id',1)->delete();
```



