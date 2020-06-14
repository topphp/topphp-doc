## 验证规则

验证规则的定义通常有两种方式，如果你使用了验证器的话，通常通过`rule`属性定义验证规则，而如果使用的是独立验证的话，则是通过`rule`方法进行定义。

## 属性定义

属性定义方式仅限于验证器，通常类似于下面的方式：

```php
<?php
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

> 系统内置了一些常用的验证规则可以满足大部分的验证需求，具体每个规则的含义参考内置规则一节。

因为一个字段可以使用多个验证规则（如上面的`age`字段定义了`number`和`between`两个验证规则），在一些特殊的情况下，为了避免混淆可以在`rule`属性中使用数组定义规则。

```php
<?php
namespace app\validate;

use think\Validate;

class User extends Validate
{
    protected $rule = [
      'name'  => ['require', 'max' => 25, 'regex' => '/^[\w|\d]\w+/'],
      'age'   => ['number', 'between' => '1,120'],
      'email' => 'email',
    ];

}
```

## 方法定义

如果使用的是独立验证（即手动调用验证类进行验证）方式的话，通常使用`rule`方法进行验证规则的设置，举例说明如下。独立验证通常使用`Facade`或者自己实例化验证类。

```php
$validate = \think\facade\Validate::rule('age', 'number|between:1,120')
->rule([
    'name'  => 'require|max:25',
    'email' => 'email'
]);

$data = [
    'name'  => 'thinkphp',
    'email' => 'thinkphp@qq.com'
];

if (!$validate->check($data)) {
    dump($validate->getError());
}
```

> `rule`方法传入数组表示批量设置规则。

`rule`方法还可以支持使用对象化的规则定义。

我们把上面的验证代码改为

```php
use think\facade\Validate;
use think\validate\ValidateRule as Rule;

$validate = Validate::rule('age', Rule::isNumber()->between([1,120]))
->rule([
    'name'  => Rule::isRequire()->max(25),
    'email' => Rule::isEmail(),
]);

$data = [
    'name'  => 'thinkphp',
    'email' => 'thinkphp@qq.com'
];

if (!$validate->check($data)) {
    dump($validate->getError());
}
```

可以对某个字段使用闭包验证，例如：

```php
$validate = Validate::rule([
    'name'  => function($value) { 
        return 'thinkphp' == strtolower($value) ? true : false;
    },
]);
```

闭包支持传入两个参数，第一个参数是当前字段的值（必须），第二个参数是所有数据（可选）。

> 如果使用了闭包进行验证，则不再支持对该字段使用多个验证规则。

闭包函数如果返回true则表示验证通过，返回false表示验证失败并使用系统的错误信息，如果返回字符串，则表示验证失败并且以返回值作为错误提示信息。

```php
$validate = Validate::rule([
    'name'  => function($value) { 
        return 'thinkphp' == strtolower($value) ? true : '用户名错误';
    },
]);
```

> 属性方式定义验证规则不支持使用对象化规则定义和闭包定义

## 全局扩展

你可以在扩展包或者应用里面全局注册验证规则，使用方法

```php
Validate::maker(function($validate) {
    $validate->extend('extra', 'extra_validate_callback');
});
```



