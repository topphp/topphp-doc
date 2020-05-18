## 简介

TopPHP 采用 ThinkPHP6 作为框架的依赖注入管理容器，支持PSR-11规范。

**我们首先了解一下 ThinkPHP6 的容器和依赖注入**

> 容器类的工作由think\Container类完成，但大多数情况我们只需要通过app助手函数或者think\App类即可容器操作，如果在服务类中可以直接调用this-&gt;app进行容器操作。

依赖注入其实本质上是指对类的依赖通过构造器完成自动注入，例如在控制器架构方法和操作方法中一旦对参数进行对象类型约束则会自动触发依赖注入，由于访问控制器的参数都来自于URL请求，普通变量就是通过参数绑定自动获取，对象变量则是通过依赖注入生成。

```php
<?php
namespace app\controller;

use think\Request;

class Index
{
    protected $request;

    public function __construct(Request $request)
    {
        $this->request = $request;
    }

    public function hello($name)
    {
        return 'Hello,' . $name . '！This is '. $this->request->action();
    }
}
```

> 依赖注入的对象参数支持多个，并且和顺序无关。

支持使用依赖注入的场景包括（但不限于）：

* 控制器架构方法；
* 控制器操作方法；
* 路由的闭包定义；
* 事件类的执行方法；
* 中间件的执行方法；

对于自定义的类以及方法，如果需要使用依赖注入，需要使用系统提供的invoke助手函数调用，例如：

```php
class Foo 
{
    public function __construct(Bar $bar)
    {
    }
}
```

如果直接`new`的话，需要手动传入`Bar`对象实例

```php
$bar = new Bar(); 
$foo = new Foo($bar);
```

如果使用容器来实例化的话，可以自动进行依赖注入。

```php
$foo = invoke('Foo');
```

如果要对某个方法支持依赖注入，可以使用

```php
class Foo 
{
    public function bar(Bar $bar)
    {
        // ...
    }
}
```

```php
$result = invoke(['Foo', 'bar']);
```

也支持对某个函数或者闭包使用依赖注入

```php
$result = invoke(function(Bar $bar) {
    // ...
});
```

## 绑定

依赖注入的类统一由容器进行管理，大多数情况下是在自动绑定并且实例化的。不过你可以随时进行手动绑定类到容器中（通常是在服务类的`register`方法中进行绑定），支持多种绑定方式。

绑定类标识

可以对已有的类库绑定一个标识（唯一），便于快速调用。

```php
// 绑定类库标识
$this->app->bind('think\Cache', 'app\common\Cache');
```

或者使用助手函数

```php
// 绑定类库标识
bind('cache', 'think\Cache');
```

> 绑定的类标识可以自己定义（只要不冲突）。

**绑定闭包**

可以绑定一个闭包到容器中

```php
bind('sayHello', function ($name) {
    return 'hello,' . $name;
});
```

**绑定实例**

也可以直接绑定一个类的实例

```php
$cache = new think\Cache;
// 绑定类实例
bind('cache', $cache);
```

**绑定至接口实现**

对于依赖注入使用接口类的情况，我们需要告诉系统使用哪个具体的接口实现类来进行注入，这个使用可以把某个类绑定到接口

```php
// 绑定think\LoggerInterface接口实现到think\Log
bind('think\LoggerInterface','think\Log');
```

使用接口作为依赖注入的类型

```php
<?php
namespace app\index\controller;

use think\LoggerInterface;

class Index
{
    public function hello(LoggerInterface $log)
    {
        $log->record('hello,world!');
    }    
}
```

**批量绑定**

在实际应用开发过程，不需要手动绑定，我们只需要在`app`目录下面定义`provider.php`文件（只能在全局定义，不支持应用单独定义），系统会自动批量绑定类库到容器中。

```php
return [
    'route'      => \think\Route::class,
    'session'    => \think\Session::class,
    'url'        => \think\Url::class,
];
```

> 绑定标识调用的时候区分大小写，系统已经内置绑定了核心常用类库，无需重复绑定

系统内置绑定到容器中的类库包括

| 系统类库 |
| :--- |


|  | 容器绑定标识 |
| :--- | :--- |
| think\App | app |
| think\Cache | cache |
| think\Config | config |
| think\Cookie | cookie |
| think\Console | console |
| think\Db | db |
| think\Debug | debug |
| think\Env | env |
| think\Event | event |
| think\Http | http |
| think\Lang | lang |
| think\Log | log |
| think\Middleware | middleware |
| think\Request | request |
| think\Response | response |
| think\Filesystem | filesystem |
| think\Route | route |
| think\Session | session |
| think\Validate | validate |
| think\View | view |

**解析**

使用`app`助手函数进行容器中的类解析调用，对于已经绑定的类标识，会自动快速实例化

```php
$cache = app('cache');
```

带参数实例化调用

```php
$cache = app('cache',['file']);
```

对于没有绑定的类，也可以直接解析

```php
$arrayItem = app('org\utils\ArrayItem');
```

> 调用和绑定的标识必须保持一致（包括大小写）

容器中已经调用过的类会自动使用单例，除非你使用下面的方式强制重新实例化。

```php
// 每次调用都会重新实例化
$cache = app('cache', [], true);
```
**对象化调用**

使用`app`助手函数获取容器中的对象实例（支持依赖注入）。

```php
$app = app();
// 判断对象实例是否存在
isset($app->cache);

// 注册容器对象实例
$app->cache = think\Cache::class;

// 获取容器中的对象实例
$cache = $app->cache;
```
也就是说，你可以在任何地方使用`app()`方法调用容器中的任何类，但大多数情况下面，我们更建议使用依赖注入。
```php
// 调用配置类
app()->config->get('app_name');
// 调用session类
app()->session->get('user_name');
```
**自动注入**

容器主要用于依赖注入，依赖注入会首先检查容器中是否注册过该对象实例，如果没有就会自动实例化，然后自动注入，例如：

我们可以给路由绑定模型对象实例

```php
Route::get('user/:id','index/Index/hello')
	->model('\app\index\model\User');
```

然后在操作方法中自动注入User模型
```php
<?php
namespace app\index\controller;

use app\index\model\User;

class Index
{

    public function hello(User $user)
    {
        return 'Hello,'.$user->name;
    }

}
```

**自定义实例化**

容器中的对象实例化支持自定义，可以在你需要依赖注入的对象中增加`__make`方法定义，例如：

如果你希望`User`模型类在依赖注入的时候 使用自定义实例化的方式，可以用下面的方法。

```php
<?php
namespace app\index\model;

use think\Model;
use think\db\Query;

class User extends Model
{
	public static function __make(Query $query)
    {
    	return (new self())->setQuery($query);
    }
}
```

**容器对象回调机制**

容器中的对象实例化之后，支持回调机制，利用该机制可以实现诸如注解功能等相关功能。

你可以通过`resolving`方法注册一个全局回调
```php
Container::getInstance()->resolving(function($instance,$container) {
    // ...
});
```
回调方法支持两个参数，第一个参数是容器对象实例，第二个参数是容器实例本身。

或者单独注册一个某个容器对象的回调
```php
Container::getInstance()->resolving(\think\Cache::class,function($instance,$container) {
    // ...
});
```

#### TopPHP 注入方式

#### 通过对象注入

#### 通过构造方法注入

#### 通过 @Inject 注解注入



