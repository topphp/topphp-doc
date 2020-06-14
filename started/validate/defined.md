## 定义

为具体的验证场景或者数据表定义好验证器类，直接调用验证类的`check`方法即可完成验证，下面是一个例子：

我们定义一个`\app\validate\User`验证器类用于`User`的验证。

```php
namespace app\validate;

use think\Validate;

class User extends Validate
{
    protected $rule = [
        'name'  =>  'require|max:25',
        'email' =>  'email',
    ];

}
```

> 可以使用下面的指令快速生成`User`验证器。
>
> ```php
> php think make:validate User
>
> ```

可以直接在验证器类中使用`message`属性定义错误提示信息，例如：

```php
namespace app\validate;

use think\Validate;

class User extends Validate
{
    protected $rule =   [
        'name'  => 'require|max:25',
        'age'   => 'number|between:1,120',
        'email' => 'email',    
    ];
    
    protected $message  =   [
        'name.require' => '名称必须',
        'name.max'     => '名称最多不能超过25个字符',
        'age.number'   => '年龄必须是数字',
        'age.between'  => '年龄只能在1-120之间',
        'email'        => '邮箱格式错误',    
    ];
    
}
```

> 如果没有定义错误提示信息，则使用系统默认的提示信息

## 数据验证

在需要进行`User`验证的控制器方法中，添加如下代码即可：

```php
<?php
namespace app\controller;

use app\validate\User;
use think\exception\ValidateException;

class Index
{
    public function index()
    {
        try {
            validate(User::class)->check([
                'name'  => 'thinkphp',
                'email' => 'thinkphp@qq.com',
            ]);
        } catch (ValidateException $e) {
            // 验证失败 输出错误信息
            dump($e->getError());
        }
    }
}
```

## 批量验证

默认情况下，一旦有某个数据的验证规则不符合，就会停止后续数据及规则的验证，如果希望批量进行验证，可以设置：

```php
<?php
namespace app\controller;

use app\validate\User;
use think\exception\ValidateException;

class Index
{
    public function index()
    {
	try {
		$result = validate(User::class)->batch(true)->check([
			'name'  => 'thinkphp',
			'email' => 'thinkphp@qq.com',
		    ]);

		if (true !== $result) {
		    // 验证失败 输出错误信息
		    dump($result);
		}
	} catch (ValidateException $e) {
            // 验证失败 输出错误信息
            dump($e->getError());
        }
    }
}
```

## 自定义验证规则

系统内置了一些常用的规则（参考后面的内置规则），如果不能满足需求，可以在验证器重添加额外的验证方法，例如：

```php
<?php
namespace app\validate;

use think\Validate;

class User extends Validate
{
    protected $rule = [
        'name'  =>  'checkName:thinkphp',
        'email' =>  'email',
    ];
    
    protected $message = [
        'name'  =>  '用户名必须',
        'email' =>  '邮箱格式错误',
    ];
    
    // 自定义验证规则
    protected function checkName($value, $rule, $data=[])
    {
        return $rule == $value ? true : '名称错误';
    }
}
```

验证方法可以传入的参数共有`5`个（后面三个根据情况选用），依次为：

* 验证数据
* 验证规则
* 全部数据（数组）
* 字段名
* 字段描述

> 自定义的验证规则方法名不能和已有的规则冲突。



