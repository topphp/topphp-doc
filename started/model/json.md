## JSON字段

可以更为方便的操作模型的JSON数据字段。

> 这里指的JSON数据包括JSON类型以及JSON格式（但并不是JSON类型字段）的数据

我们修改下User模型类

```php
<?php
namespace app\model;

use think\Model;
class User extends Model
{
	// 设置json类型字段
	protected $json = ['info'];
}
```

定义后，可以进行如下JSON数据操作。

## 写入JSON数据

使用数组方式写入JSON数据：

```php
$user = new User;
$user->name = 'thinkphp';
$user->info = [
    'email'    => 'thinkphp@qq.com',
    'nickname '=> '流年',
];
$user->save();
```

使用对象方式写入JSON数据

```php
$user = new User;
$user->name = 'thinkphp';
$info = new \StdClass();
$info->email = 'thinkphp@qq.com';
$info->nickname = '流年';
$user->info = $info;
$user->save();
```

## 查询JSON数据

```php
$user = User::find(1);
echo $user->name; // thinkphp
echo $user->info->email; // thinkphp@qq.com
echo $user->info->nickname; // 流年
```

查询条件为JSON数据

```php
$user = User::where('info->nickname','流年')->find();
echo $user->name; // thinkphp
echo $user->info->email; // thinkphp@qq.com
echo $user->info->nickname; // 流年
```

如果你需要查询的JSON属性是整型类型的话，可以在模型类里面定义JSON字段的属性类型，就会自动进行相应类型的参数绑定查询。

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
	// 设置json类型字段
	protected $json = ['info'];
    
       // 设置JSON字段的类型
       protected $jsonType = [
    	   'info->user_id'   =>   'int'
       ];
}
```

没有定义类型的属性默认为字符串类型，因此字符串类型的属性可以无需定义。

可以设置模型的`JSON`数据返回数组，只需要在模型设置`jsonAssoc`属性为`true`。

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
	// 设置json类型字段
	protected $json = ['info'];
    
       // 设置JSON数据返回数组
       protected $jsonAssoc = true;
}
```

设置后，查询代码调整为：

```php
$user = User::find(1);
echo $user->name; // thinkphp
echo $user->info['email']; // thinkphp@qq.com
echo $user->info['nickname']; // 流年
```

## 更新JSON数据

```php
$user = User::find(1);
$user->name = 'kancloud';
$user->info->email = 'kancloud@qq.com';
$user->info->nickname = 'kancloud';
$user->save();
```

如果设置模型的`JSON`数据返回数组，那么更新操作需要调整如下。

```php
$user = User::find(1);
$user->name = 'kancloud';
$info['email'] = 'kancloud@qq.com';
$info['nickname'] = 'kancloud';
$user->info = $info;
$user->save();
```



