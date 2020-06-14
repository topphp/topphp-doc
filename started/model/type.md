## 类型转换

支持给字段设置类型自动转换，会在写入和读取的时候自动进行类型转换处理，例如：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    protected $type = [
        'status'    =>  'integer',
        'score'     =>  'float',
        'birthday'  =>  'datetime',
        'info'      =>  'array',
    ];
}
```

下面是一个类型自动转换的示例：

```php
$user = new User;
$user->status = '1';
$user->score = '90.50';
$user->birthday = '2015/5/1';
$user->info = ['a'=>1,'b'=>2];
$user->save();
var_dump($user->status); // int 1
var_dump($user->score); // float 90.5;
var_dump($user->birthday); // string '2015-05-01 00:00:00'
var_dump($user->info);// array (size=2) 'a' => int 1  'b' => int 2
```

数据库查询默认取出来的数据都是字符串类型，如果需要转换为其他的类型，需要设置，支持的类型包括如下类型：

## `integer`

设置为integer（整型）后，该字段写入和输出的时候都会自动转换为整型。

## `float`

该字段的值写入和输出的时候自动转换为浮点型。

## `boolean`

该字段的值写入和输出的时候自动转换为布尔型。

## `array`

如果设置为强制转换为`array`类型，系统会自动把数组编码为json格式字符串写入数据库，取出来的时候会自动解码。

## `object`

该字段的值在写入的时候会自动编码为json字符串，输出的时候会自动转换为`stdclass`对象。

## `serialize`

指定为序列化类型的话，数据会自动序列化写入，并且在读取的时候自动反序列化。

## `json`

指定为`json`类型的话，数据会自动`json_encode`写入，并且在读取的时候自动`json_decode`处理。

## `timestamp`

指定为时间戳字段类型的话，该字段的值在写入时候会自动使用`strtotime`生成对应的时间戳，输出的时候会自动转换为`dateFormat`属性定义的时间字符串格式，默认的格式为`Y-m-d H:i:s`，如果希望改变其他格式，可以定义如下：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    protected $dateFormat = 'Y/m/d';
    protected $type = [
        'status'    =>  'integer',
        'score'     =>  'float',
        'birthday'  =>  'timestamp',
    ];
}
```

或者在类型转换定义的时候使用：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    protected $type = [
        'status'    =>  'integer',
        'score'     =>  'float',
        'birthday'  =>  'timestamp:Y/m/d',
    ];
}
```

然后就可以

```php
$user = User::find(1);
echo $user->birthday; // 2015/5/1
```

## `datetime`

和`timestamp`类似，区别在于写入和读取数据的时候都会自动处理成时间字符串`Y-m-d H:i:s`的格式。

