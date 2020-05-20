## 事件系统

ThinkPHP 6 的事件系统可以看成是`5.1`版本行为系统的升级版，事件系统相比行为系统强大的地方在于事件本身可以是一个类，并且可以更好的支持事件订阅者。

事件相比较中间件的优势是事件比中间件更加精准定位（或者说粒度更细），并且更适合一些业务场景的扩展。例如，我们通常会遇到用户注册或者登录后需要做一系列操作，通过事件系统可以做到不侵入原有代码完成登录的操作扩展，降低系统的耦合性的同时，也降低了BUG的可能性。

> 事件系统的所有操作都通过`think\facade\Event`类进行静态调用

## 定义事件

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



