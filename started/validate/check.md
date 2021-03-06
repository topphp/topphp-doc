## 全局验证器中间件 Check

`TopPHP`骨架默认的 index admin 应用 与 api 多版本应用已配置好全局验证器Check中间件，开发者可以不用关心中间件的配置问题，只需要在对应的应用下validate目录添加验证器文件与配置规则即可，骨架会自动验证并返回信息。

以`admin`应用目录举例，开发者如果创建新的应用目录，也请参照`admin`应用目录的规则创建。

```
www  WEB部署目录（或者子目录）
├─app           应用目录
│  ├─admin                  后台管理应用
│  │  ├─common.php          函数文件
│  │  ├─controller          控制器目录 -- Tips：数据入参的基本验证处理与发送客户端响应，业务逻辑应尽量移步到 service 下
│  │  │  ├─Base.php         控制器基础抽象类（所有新建控制器都需要继承此类）
│  │  │  ├─Index.php        Index控制器
│  │  │  └─ ...             更多控制器
│  │  │
│  │  ├─model               模型目录 -- Tips：一般应仅用于对数据库的操作，业务逻辑应尽量移步到 service 下
│  │  │  ├─DemoDao.php      示例模型（1、所有新建模型需继承 app\model\entity 下对应的模型实体类）
│  │  │  └─ ...             更多模型（2、所有新建模型都应以 xxxDao.php 命名，推荐使用TopPHP的 topphp/topphp-generate 组件自动生成）
│  │  │
│  │  ├─view                视图目录（如果需要可自行创建，骨架已自动加载了 topthink/think-view 视图组件）
│  │  ├─config              配置目录
│  │  │  ├─middleware.php   中间件配置（骨架默认配置了全局验证器 Check 中间件和 Auth 权限中间件，权限中间件内容需要开发者自行填充业务）
│  │  │  └─ ...             更多配置
│  │  │
│  │  ├─middleware          admin应用中间件目录
│  │  │  ├─Auth.php         Auth权限中间件（骨架已在控制器基础抽象类 Base.php 配置好了该中间件，只需要开发者自行填充此中间件业务即可实现控制器权限控制）
│  │  │  └─ ...             更多中间件
│  │  │
│  │  ├─route               路由目录
│  │  ├─service             服务目录 -- Tips：用于编写业务逻辑代码，或应用于微服务场景
│  │  │  ├─DemoService.php  示例服务文件（服务文件名应统一以 xxxService.php 命名，且默认不依赖任何模块）
│  │  │  └─ ...             更多服务
│  │  │
│  │  ├─validate            验证器目录 -- Tips：用于配置指定的控制器验证器验证规则，与TopPHP的 Check 全局验证器配合使用
│  │  │  ├─IndexCheck.php   Index控制器验证类（验证器名称必须统一以 xxxCheck.php 命名，Check 全局验证器会自动触发验证，并以json code形式响应）
│  │  │  └─ ...             更多验证器
│  │  │
│  │  └─ ...                更多类库目录
```

上面我们看到`middleware`目录是存放当前应用的中间件的，所以没有找到`Check`中间件，`Check`中间件放在`app\middleware`目录下，作为全局中间件使用，在应用下的`config`目录下存在一个`middleware.php`的配置文件：

```php
<?php
// admin中间件配置
return [
    // 别名或分组
    'alias'    => [
        "Auth" => app\admin\middleware\Auth::class,
        "Check" => app\middleware\Check::class,
    ],
    // 优先级设置，此数组中的中间件会按照数组中的顺序优先执行
    'priority' => [
        app\middleware\Check::class,
        app\admin\middleware\Auth::class,
    ],
];
```

此文件即是配置当前应用的中间件的文件，我们可以看到这里引用了`Check`中间件，所以才会在当前应用生效。打开`app\admin\controller\Base.php`即可看到这里通过控制器中间件属性`$middleware`引用了`Check`中间件：

```php
declare(strict_types=1);

namespace app\admin\controller;

use app\BaseController;

abstract class Base extends BaseController
{
    /**
     * 定义业务中间件
     * @var array
     */
    protected $middleware = ['Check','Auth'];

    // Admin应用基础业务逻辑（所有方法需定义protected关键词）
}
```

至此，`Check`中间件的定义与引用完成。

### 使用

`app\admin\validate`目录即是存放验证器中间件的目录，这与`ThinkPHP`的定义验证器规则是一致的，但是**使用**`Check`**全局验证器功能，必须将验证器配置文件以**`xxxCheck.php`**形式命名，否则将不会生效**。

定义规则与`ThinkPHP`的验证场景定义规则一致：

```php
namespace app\admin\validate;

use think\Validate;

class IndexCheck extends Validate
{
    /**
     * 定义验证规则
     * 格式：'字段名'    =>    ['规则1','规则2'...]
     *
     * @var array
     */
    protected $rule = [

    ];

    /**
     * 定义错误信息
     * 格式：'字段名.规则名'    =>    '错误信息'
     *
     * @var array
     */
    protected $message = [

    ];

    /**
     * 定义验证场景
     *
     * @var array
     */
    protected $scene = [

    ];
}
```

`$rule`验证规则与`ThinkPHP`定义规则一致，在此不做赘述，`$message`错误信息除了兼容`ThinkPHP`的错误信息定义方式外，还支持数组形式的定义方式：

```php
namespace app\admin\validate;

use think\Validate;

class UserCheck extends Validate
{
    protected $rule = [
        'name'  => 'require|max:25',
        'age'   => 'number|between:1,120',
        'email' => 'email',
    ];

    protected $message = [
        'name.require' => ['code' => 20001, 'message' => '名称必须'],
        'name.max'     => ['code' => 20002, 'message' => '名称最多不能超过25个字符'],
        'age.number'   => ['code' => 20003, 'message' => '年龄必须是数字'],
        'age.between'  => ['code' => 20004, 'message' => '年龄必须在1~120之间'],
        'email'        => ['code' => 20005, 'message' => '邮箱格式错误'],
    ];

    protected $scene = [
        "register" => ["name", "age", "email"]
    ];
}
```

> 注意上面的错误信息`key`值与`ThinkPHP`的有所区别，`ThinkPHP`的是`msg`，使用`Check`中间件定义就要写成`message`。为了拥抱`PHP严格模式`错误信息的内容必须是`string`类型，不允许为`int`型。错误码`code`的类型推荐`int`型。

上面如果验证失败，将会返回类似如下`json`数据：

```json
{
    "code":20001,
    "message":"名称必须",
    "data":[

    ],
    "operate":"admin/User/register"
}
```

如果你的项目对于`code`码没有那么高的需求，还可以直接填写字符串形式的错误信息：

```php
namespace app\admin\validate;

use think\Validate;

class UserCheck extends Validate
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

    protected $scene = [
        "register" => ["name", "age", "email"]
    ];
}
```

验证失败将会统一返回`40000`的`code`错误码。

> 如果你使用`ThinkPHP`的注解验证器是不支持数组形式定义的，注解验证器，请查看注解验证章节。

`$scene`验证场景参数我们规定为：

```
'操作方法名(actionName)'
```

如：上面的注册方法，即是`register`。如果你是多层级控制器，那么命名规则就是：

```
'层级名(layered).操作方法名(actionName)'
```

> 如你控制器下有不需要验证的操作方法，请务必定义场景值对应的操作方法为空字符串数组，如下面的`index`方法。

```php
namespace app\admin\validate;

use think\Validate;

class UserCheck extends Validate
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

    protected $scene = [
        "index"    => [''],
        "register" => ["name", "age", "email"]
    ];
}
```

> `Check`全局验证器中间件，默认会自动触发验证，你无须再在控制器中调用任何其他的验证方法。

如果你有需要单独验证的场景，比如当入参`type`类型为`1`的时候才需要验证`password`字段，其他情况下，不验证，我们可以在验证器文件中这样定义场景值：

```php
namespace app\admin\validate;

use think\Validate;

class UserCheck extends Validate
{
    protected $rule = [
        'name'     => 'require|max:25',
        'age'      => 'number|between:1,120',
        'email'    => 'email',
        'password' => 'require|between:6,20',
    ];

    protected $message = [
        'name.require'      => '名称必须',
        'name.max'          => '名称最多不能超过25个字符',
        'age.number'        => '年龄必须是数字',
        'age.between'       => '年龄必须在1~120之间',
        'email'             => '邮箱格式错误',
        'password.require'  => '密码必填',
        'password.between'  => '密码必须为6~20位',
    ];

    protected $scene = [
        "index"             => [''],
        "register"          => ["name", "age", "email"],
        "register@password" => ["password"],
    ];
}
```

我们通过在验证场景中按照如下规则定义单独验证的场景：

```
操作方法名(actionName)@字段名 => ['字段验证规则key']
```

上面的定义方式需注意 @ 后字段名区分大小写，配置好后，默认情况下访问`register`操作是不会自动验证`password`字段的，当我们按照条件需要（如：`type`参数类型为`1`时）验证时，就在控制器需要验证的地方调用`checkOneRequestParam`公共方法来进行验证。

> checkOneRequestParam \( '字段名', '请求方式' \)

`checkOneRequestParam`方法会根据验证器文件中定义的单独验证场景和规则，进行单独验证某一个字段，请求方式支持`get`，`post`，`put`，`patch`，`delete`。可以使用`MethodEnum`枚举类来传入对应的请求方式，如：

```php
checkOneRequestParam("password",MethodEnum::POST);
```

> `checkOneRequestParam`方法验证成功会返回`true`，验证失败会自动向客户端抛出响应错误信息，如：

```json
{
    "code":40000,
    "message":"密码必填",
    "data":[

    ],
    "operate":"admin/User/register"
}
```



