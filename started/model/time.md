## 自动时间戳

系统支持自动写入创建和更新的时间戳字段（默认关闭），有两种方式配置支持。

第一种方式是全局开启，在数据库配置文件中进行设置：

```php
// 开启自动写入时间戳字段
'auto_timestamp' => true,
```

第二种是在需要的模型类里面单独开启：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    protected $autoWriteTimestamp = true;
}
```

又或者首先在数据库配置文件中全局开启，然后在个别不需要使用自动时间戳写入的模型类中单独关闭：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    protected $autoWriteTimestamp = false;
}
```

一旦配置开启的话，会自动写入`create_time`和`update_time`两个字段的值，默认为整型（`int`），如果你的时间字段不是`int`类型的话，可以直接使用：

```php
// 开启自动写入时间戳字段
'auto_timestamp' => 'datetime',
```

或者

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    protected $autoWriteTimestamp = 'datetime';
}
```

默认的创建时间字段为`create_time`，更新时间字段为`update_time`，支持的字段类型包括`timestamp/datetime/int`。

写入数据的时候，系统会自动写入`create_time`和`update_time`字段，而不需要定义修改器，例如：

```php
$user = new User();
$user->name = 'thinkphp';
$user->save();
echo $user->create_time; // 输出类似 2016-10-12 14:20:10
echo $user->update_time; // 输出类似 2016-10-12 14:20:10
```

> 时间字段的自动写入仅针对模型的写入方法，如果使用数据库的更新或者写入方法则无效。

> 时间字段输出的时候会自动进行格式转换，如果不希望自动格式化输出，可以把数据库配置文件的`datetime_format`参数值改为`false`

`datetime_format`参数支持设置为一个时间类名，这样便于你进行更多的时间处理，例如：

```php
// 设置时间字段的格式化类
'datetime_format' => '\org\util\DateTime',
```

该类应该包含一个`__toString`方法定义以确保能正常写入数据库。

如果你的数据表字段不是默认值的话，可以按照下面的方式定义：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    // 定义时间戳字段名
    protected $createTime = 'create_at';
    protected $updateTime = 'update_at';
}
```

下面是修改字段后的输出代码：

```php
$user = new User();
$user->name = 'thinkphp';
$user->save();
echo $user->create_at; // 输出类似 2016-10-12 14:20:10
echo $user->update_at; // 输出类似 2016-10-12 14:20:10
```

如果你只需要使用`create_time`字段而不需要自动写入`update_time`，则可以单独关闭某个字段，例如：

```php
namespace app\model;

use think\Model;

class User extends Model 
{
    // 关闭自动写入update_time字段
    protected $updateTime = false;
}
```

支持动态关闭时间戳写入功能，例如你希望更新阅读数的时候不修改更新时间，可以使用`isAutoWriteTimestamp`方法：

```php
$user = User::find(1);
$user->read +=1;
$user->isAutoWriteTimestamp(false)->save();
```



