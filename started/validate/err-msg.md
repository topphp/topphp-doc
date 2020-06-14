## 错误信息

验证规则的错误提示信息有三种方式可以定义，如下：

## 使用默认的错误提示信息

如果没有定义任何的验证提示信息，系统会显示默认的错误信息，例如：

```php
namespace app\validate;

use think\Validate;

class User extends Validate
{
    protected $rule = [
      'name'  => 'require|max:25',
      'age'   => 'number|between:1,120',
      'email' => 'email',
    ];

}
```

```php
$data = [
    'name'  => 'thinkphp',
    'age'   => 121,
    'email' => 'thinkphp@qq.com',
];

$validate = new \app\validate\User;
$result = $validate->check($data);

if(!$result){
    echo $validate->getError();
}
```

会输出`age只能在 1 - 120 之间`。

可以给`age`字段设置中文名，例如：

```php
namespace app\validate;

use think\Validate;

class User extends Validate
{
    protected $rule = [
      'name'  => 'require|max:25',
      'age|年龄'   => 'number|between:1,120',
      'email' => 'email',
    ];

}
```

会输出`年龄只能在 1 - 120 之间`。

## 单独定义提示信息

如果要输出自定义的错误信息，可以定义`message`属性：

```php
namespace app\validate;

use think\Validate;

class User extends Validate
{
    protected $rule = [
      'name'  => 'require|max:25',
      'age'   => 'number|between:1,120',
      'email' => 'email',
    ];

    protected $message = [
      'name.require' => '名称必须',
      'name.max'     => '名称最多不能超过25个字符',
      'age.number'   => '年龄必须是数字',
      'age.between'  => '年龄必须在1~120之间',
      'email'        => '邮箱格式错误',
    ];
}
```

```php
$data = [
    'name'  => 'thinkphp',
    'age'   => 121,
    'email' => 'thinkphp@qq.com',
];

$validate = new \app\validate\User;
$result = $validate->check($data);

if(!$result){
    echo $validate->getError();
}
```

会输出`年龄必须在1~120之间`。

错误信息可以支持数组定义，并且通过JSON方式传给前端。

```php
namespace app\validate;

use think\Validate;

class User extends Validate
{
    protected $rule = [
      'name'  => 'require|max:25',
      'age'   => 'number|between:1,120',
      'email' => 'email',
    ];

    protected $message = [
      'name.require' => ['code' => 1001, 'msg' => '名称必须'],
      'name.max'     => ['code' => 1002, 'msg' => '名称最多不能超过25个字符'],
      'age.number'   => ['code' => 1003, 'msg' => '年龄必须是数字'],
      'age.between'  => ['code' => 1004, 'msg' => '年龄必须在1~120之间'],
      'email'        => ['code' => 1005, 'msg' =>'邮箱格式错误'],
    ];
}
```

## 使用多语言

验证信息提示支持多语言功能，你只需要给相关错误提示信息定义语言包，例如：

```php
namespace app\validate;

use think\Validate;

class User extends Validate
{
    protected $rule = [
      'name'  => 'require|max:25',
      'age'   => 'number|between:1,120',
      'email' => 'email',
    ];

    protected $message = [
      'name.require' => 'name_require',
      'name.max'     => 'name_max',
      'age.number'   => 'age_number',
      'age.between'  => 'age_between',
      'email'        => 'email_error',
    ];
}
```

你可以在语言包文件中添加下列定义：

```php
'name_require '	=>	'姓名必须',
'name_max' 		=>	'姓名最大长度不超过25个字符',
'age_between'	=>	'年龄必须在1~120之间',
'age_number'	=>	'年龄必须是数字',
'email_error'	=>	'邮箱格式错误',
```

> 系统内置的验证错误提示均支持多语言（参考框架目录下的`lang/zh-cn.php`语言定义文件）。



