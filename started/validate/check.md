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