## 注解验证器

ThinkPHP支持使用注解方式定义路由和验证，需要安装额外的扩展：

```
composer require topthink/think-annotation
```

> `注解验证器`功能依赖`注解路由`，所以只有定义了`注解路由`，才能使`注解验证器`生效

然后可以直接在控制器类的方法注释中定义，例如：

```php
<?php
namespace app\admin\controller;

use think\annotation\Route;
use think\annotation\route\Validate;
use app\admin\validate\IndexCheck;

class Index extends Base
{
    /**
     * @Validate(IndexCheck::class,scene="hello",batch="true")
     * @return mixed
     * @Route("hello")
     */
    public function hello()
    {
        return 'hello, TP6 Annotation Validate';
    }
}
```

`@Route("hello/:name")`和`@Validate(IndexCheck::class)`就是注解路由和验证器的内容，请务必注意注释的规范，不能在注解路由里面使用单引号，否则可能导致注解路由解析失败，可以利用IDE生成规范的注释。如果你使用`PHPStorm`的话，建议安装`PHP Annotations`插件：[https://plugins.jetbrains.com/plugin/7320-php-annotations](https://plugins.jetbrains.com/plugin/7320-php-annotations)，可以支持注解的自动完成。

然后需要声明上面引用验证器类，例如：

```php
<?php

namespace app\admin\validate;

use think\Validate;

class IndexCheck extends Validate
{
    protected $rule = [
        'name' => 'require'
    ];

   protected $message = [
       'name.require' => '姓名必须填写',
   ];

    protected $scene = [
        'hello' => ['name'],
    ];
}
```

> 该方式定义的路由在调试模式下面实时生效，部署模式则在第一次访问的时候生成注解缓存。
>
> 注解验证器不支持自定义返回`code`码。如果你有自定义`code`码的需求，推荐使用TopPHP骨架自带的全局`Check`验证器功能。

### 注解验证器参数说明

> `value`参数，可以不写`value=`，直接抒写值就可以了

| 参数名 | 参数类型 | 参数默认值 | 参数说明 |
| :--- | :--- | :--- | :--- |
| value | string |  | 验证器 |
| scene | string |  | 验证场景 |
| batch | bool | true | 统一验证：true=是，flase=不是 |
| message | array | \[\] | 错误内容 |

### 错误访问示例：

```
http://127.0.0.1:9501/admin/hello
```

页面输出

```js
{"code":40000,"message":"姓名必须填写","data":[],"operate":"admin\/Index\/hello"}
```

### 正确访问示例：

```
http://127.0.0.1:9501/admin/hello?name=zhangsan
```

页面输出

```
hello, TP6 Annotation Validate
```



