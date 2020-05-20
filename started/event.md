## 事件系统

ThinkPHP 6 的事件系统可以看成是`5.1`版本行为系统的升级版，事件系统相比行为系统强大的地方在于事件本身可以是一个类，并且可以更好的支持事件订阅者。

事件相比较中间件的优势是事件比中间件更加精准定位（或者说粒度更细），并且更适合一些业务场景的扩展。例如，我们通常会遇到用户注册或者登录后需要做一系列操作，通过事件系统可以做到不侵入原有代码完成登录的操作扩展，降低系统的耦合性的同时，也降低了BUG的可能性。

> 事件系统的所有操作都通过`think\facade\Event`类进行静态调用

###定义事件

事件系统使用了观察者模式，提供了解耦应用的更好方式。在你需要监听事件的位置，例如下面我们在用户完成登录操作之后添加如下事件触发代码：

```php
// 触发UserLogin事件 用于执行用户登录后的一系列操作
Event::trigger('UserLogin');
```

或者使用助手函数

```php
event('UserLogin');
```

这里`UserLogin`表示一个事件标识，如果你定义了单独的事件类，你可以使用事件类名（甚至可以传入一个事件类实例）。

```php
// 直接使用事件类触发
event('app\event\UserLogin');
```

事件类可以通过命令行快速生成

```
php think make:event UserLogin
```

默认会生成一个`app\event\UserLogin`事件类，也可以指定完整类名生成。

我们可以给事件类添加方法

```php
namespace app\event;

use app\model\User;

class UserLogin
{
    public $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }
}
```

一般事件类无需继承任何其它类。

你可以给事件类绑定一个事件标识，一般建议直接在应用的`event.php`事件定义文件中批量绑定。

```php
return [
    'bind'    =>    [
        'UserLogin' => 'app\event\UserLogin',
        // 更多事件绑定
    ],
];
```

如果你需要动态绑定，可以使用

```php
Event::bind(['UserLogin' => 'app\event\UserLogin']);
```

> ThinkPHP的事件系统不依赖事件类，如果没有额外的需求，仅通过事件标识也可以使用，省去定义事件类的麻烦。

如果你没有定义事件类的话，则无需绑定。对于大部分的场景，可能确实不需要定义事件类。

你可以在`event`方法中传入一个事件参数

```php
// user是当前登录用户对象实例
event('UserLogin', $user);
```

如果是定义了事件类，可以直接传入事件对象实例

```php
// user是当前登录用户对象实例
event(new UserLogin($user));
```

**事件监听**

你可以手动注册一个事件监听

```php
Event::listen('UserLogin', function($user) {
    // 
});
```

或者使用监听类来执行监听

```php
Event::listen('UserLogin', 'app\listener\UserLogin');
```

可以通过命令行快速生成一个事件监听类

```
php think make:listener UserLogin
```

默认会生成一个`app\listener\UserLogin`事件监听类，也可以指定完整类名生成。

事件监听类只需要定义一个`handle`方法，支持依赖注入。

```php
<?php
namespace app\listener;

class UserLogin
{
    public function handle($user)
    {
        // 事件监听处理
    }   
}
```

在`handle`方法中如果返回了`false`，则表示监听中止，将不再执行该事件后面的监听。

一般建议直接在事件定义文件中定义对应事件的监听。

```php
return [
    'bind'    =>    [
        'UserLogin' => 'app\event\UserLogin',
        // 更多事件绑定
    ],
    'listen'  =>    [
        'UserLogin'    =>    ['app\listener\UserLogin'],
        // 更多事件监听
    ],
];
```

**事件订阅**

可以通过事件订阅机制，在一个监听器中监听多个事件，例如通过命令行生成一个事件订阅者类，

```
php think make:subscribe User
```

默认会生成`app\subscribe\User`类，或者你可以指定完整类名生成。

然后你可以在事件订阅类中添加不同事件的监听方法，例如。

```php
<?php
namespace app\subscribe;

class User
{
    public function onUserLogin($user)
    {
        // UserLogin事件响应处理
    }

    public function onUserLogout($user)
    {
        // UserLogout事件响应处理
    }
}
```

监听事件的方法命名规范是`on`+事件标识（驼峰命名），如果希望统一添加事件前缀标识，可以定义`eventPrefix`属性。

```php
<?php
namespace app\subscribe;

class User
{
    protected $eventPrefix = 'User';

    public function onLogin($user)
    {
        // UserLogin事件响应处理
    }

    public function onLogout($user)
    {
        // UserLogout事件响应处理
    }
}
```

如果希望自定义订阅方式（或者方法规范），可以定义`subscribe`方法实现。

```php
<?php
namespace app\subscribe;

use think\Event;

class User
{
    public function onUserLogin($user)
    {
        // UserLogin事件响应处理
    }

    public function onUserLogout($user)
    {
        // UserLogout事件响应处理
    }

    public function subscribe(Event $event)
    {
        $event->listen('UserLogin', [$this,'onUserLogin']);
        $event->listen('UserLogout',[$this,'onUserLogout']);
    }
}
```

然后在事件定义文件注册事件订阅者

```php
return [
    'bind'    =>    [
        'UserLogin' => 'app\event\UserLogin',
        // 更多事件绑定
    ],
    'listen'  =>    [
        'UserLogin'    =>    ['app\listener\UserLogin'],
        // 更多事件监听
    ],
    'subscribe'    =>    [
       'app\subscribe\User',
        // 更多事件订阅
    ],
];
```

如果需要动态注册，可以使用

```php
Event::subscribe('app\subscribe\User');
```

**内置事件**

内置的系统事件包括：

| 事件 | 描述 | 参数 |
| :--- | :--- | :--- |
| AppInit | 应用初始化标签位 | 无 |
| HttpRun | 应用开始标签位 | 无 |
| HttpEnd | 应用结束标签位 | 当前响应对象实例 |
| LogWrite | 日志write方法标签位 | 当前写入的日志信息 |
| RouteLoaded | 路由加载完成 | 无 |

> `AppInit`事件定义必须在全局事件定义文件中定义，其它事件支持在应用的事件定义文件中定义。

原来`5.1`的一些行为标签已经废弃，所有取消的标签都可以使用中间件更好的替代。可以把中间件看成处理请求以及响应输出相关的特殊事件。事实上，中间件的`handler`方法只是具有特殊的参数以及返回值而已。

数据库操作的回调也称为查询事件，是针对数据库的CURD操作而设计的回调方法，主要包括：

| 事件 | 描述 |
| :--- | :--- |
| before\_select | `select`查询前回调 |
| before\_find | `find`查询前回调 |
| after\_insert | `insert`操作成功后回调 |
| after\_update | `update`操作成功后回调 |
| after\_delete | `delete`操作成功后回调 |

> 查询事件的参数就是当前的查询对象实例。

模型事件包含：

| 钩子 | 对应操作 |
| :--- | :--- |
| after\_read | 查询后 |
| before\_insert | 新增前 |
| after\_insert | 新增后 |
| before\_update | 更新前 |
| after\_update | 更新后 |
| before\_write | 写入前 |
| after\_write | 写入后 |
| before\_delete | 删除前 |
| after\_delete | 删除后 |

`before_write`和`after_write`事件无论是新增还是更新都会执行。

> 模型事件方法的参数就是当前的模型对象实例。



