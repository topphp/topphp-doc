## 中间件

中间件主要用于拦截或过滤应用的HTTP请求，并进行必要的业务处理。

> ThinkPHP 6 部分核心功能使用中间件处理，你可以灵活关闭。包括Session功能、请求缓存和多语言功能。

### 定义中间件

可以通过命令行指令快速生成中间件

```
php think make:middleware Auth
```

这个指令会 `app/middleware` 目录下面生成一个`Auth`中间件。

```php
<?php

namespace app\middleware;

class Auth
{
    public function handle($request, \Closure $next)
    {
        if ($request->param('name') == 'think') {
            return redirect('index/think');
        }

        return $next($request);
    }
}
```

中间件的入口执行方法必须是`handle`方法，而且第一个参数是`Request`对象，第二个参数是一个闭包。

> 中间件`handle`方法的返回值必须是一个`Response`对象，推荐使用TopPHP提供的`SendMsg`类进行返回。

如果需要让代码在中间件中不通过，而直接发送响应给客户端，请使用如下方式：

```php
<?php

namespace app\middleware;

class Auth
{
    public function handle($request, \Closure $next)
    {
        if ($request->param('name') == 'error') {
            return SendMsg::jsonThrow("错误信息", "错误码，如: 40000");
        }

        return $next($request);
    }
}
```

在这个中间件中我们判断当前请求的`name`参数等于`error`的时候进行错误提示。否则，请求将进一步传递到应用中。要让请求继续传递到应用程序中，只需使用 `$request` 作为参数去调用回调函数 `$next` 。

> 在某些需求下，可以使用第三个参数传入额外的参数。

```php
<?php

namespace app\middleware;

class Auth
{
    public function handle($request, \Closure $next, $name)
    {
        if ($name == 'error') {
            return SendMsg::jsonThrow("错误信息", "错误码，如: 40000");
        }

        return $next($request);
    }
}
```

### 结束调度

中间件支持定义请求结束前的回调机制，你只需要在中间件类中添加`end`方法。

```php
    public function end(\think\Response $response)
    {
        // 回调行为
    }
```

> 注意，在`end`方法里面不能有任何的响应输出。因为回调触发的时候请求响应输出已经完成了。

### 前置/后置中间件

中间件是在请求具体的操作之前还是之后执行，完全取决于中间件的定义本身。

下面是一个前置行为的中间件

```php
<?php

namespace app\middleware;

class Before
{
    public function handle($request, \Closure $next)
    {
        // 添加中间件执行代码

        return $next($request);
    }
}
```

下面是一个后置行为的中间件

```php
<?php

namespace app\middleware;

class After
{
    public function handle($request, \Closure $next)
    {
        $response = $next($request);

        // 添加中间件执行代码

        return $response;
    }
}
```

中间件方法同样也可以支持依赖注入。

来个比较实际的例子，我们需要判断当前浏览器环境是在微信或支付宝

```php
namespace app\middleware;

/**
 * 访问环境检查，是否是微信或支付宝等
 */
class InAppCheck
{
    public function handle($request, \Closure $next)
    {
        if (preg_match('~micromessenger~i', $request->header('user-agent'))) {
            $request->InApp = 'WeChat';
        } else if (preg_match('~alipay~i', $request->header('user-agent'))) {
            $request->InApp = 'Alipay';
        }
        return $next($request);
    }
}
```

然后在你的移动版的应用里添加一个`middleware.php`文件  
例如：`/path/app/mobile/middleware.php`

```php
return [
    app\middleware\InAppCheck::class,
];
```

然后在你的`controller`中可以通过`request()->InApp`获取相关的值

### 定义中间件别名

可以直接在应用配置目录下的`middleware.php`中先预定义中间件（其实就是增加别名标识），例如：

```php
return [
    'alias' => [
        'auth'  => app\middleware\Auth::class,
        'check' => app\middleware\Check::class,
    ],
];
```

可以支持使用别名定义一组中间件，例如：

```php
return [
    'alias' => [
        'check' => [
            app\middleware\Auth::class,
            app\middleware\Check::class,
        ],
    ],
];
```

### 注册中间件

新版的中间件分为全局中间件、应用中间件（多应用模式下有效）、路由中间件以及控制器中间件四个组。执行顺序分别为：

> 全局中间件-&gt;应用中间件-&gt;路由中间件-&gt;控制器中间件

### 全局中间件

全局中间件在`app`目录下面`middleware.php`文件中定义，使用下面的方式：

```php
<?php

return [
    \app\middleware\Auth::class,
    'check',
    'Hello',
];
```

中间件的注册应该使用完整的类名，如果已经定义了中间件别名（或者分组）则可以直接使用。

全局中间件的执行顺序就是定义顺序。可以在定义全局中间件的时候传入中间件参数，支持两种方式传入。

```php
<?php

return [
    [\app\http\middleware\Auth::class, 'admin'],
    'Check',
    ['hello','thinkphp'],
];
```

上面的定义表示 给`Auth`中间件传入`admin`参数，给`Hello`中间件传入`thinkphp`参数。

### 应用中间件

如果你使用了多应用模式，则支持应用中间件定义，你可以直接在应用目录下面增加middleware.php文件，定义方式和全局中间件定义一样，只是只会在该应用下面生效。

### 路由中间件

最常用的中间件注册方式是注册路由中间件

```php
Route::rule('hello/:name','hello')
    ->middleware(\app\middleware\Auth::class);
```

支持注册多个中间件

```php
Route::rule('hello/:name','hello')
    ->middleware([\app\middleware\Auth::class, \app\middleware\Check::class]);
```

然后，直接使用下面的方式注册中间件

```php
Route::rule('hello/:name','hello')
    ->middleware('check');
```

支持对路由分组注册中间件

```php
Route::group('hello', function(){
    Route::rule('hello/:name','hello');
})->middleware('auth');
```

支持对某个域名注册中间件

```php
Route::domain('admin', function(){
    // 注册域名下的路由规则
})->middleware('auth');
```

如果需要传入额外参数给中间件，可以使用

```php
Route::rule('hello/:name','hello')
    ->middleware('auth', 'admin');
```

如果需要定义多个中间件，使用数组方式

```php
Route::rule('hello/:name','hello')
    ->middleware([Auth::class, 'Check']);
```

可以统一传入同一个额外参数

```php
Route::rule('hello/:name','hello')
    ->middleware(['auth', 'check'], 'admin');
```

或者分开多次调用，指定不同的参数

```php
Route::rule('hello/:name','hello')
    ->middleware('auth', 'admin')
        ->middleware('hello', 'thinkphp');
```

如果你希望某个路由中间件是全局执行（不管路由是否匹配），可以不需要在路由里面定义，支持直接在路由配置文件中定义，例如在`config/route.php`配置文件中添加：

```php
'middleware'    =>    [
    app\middleware\Auth::class,
    app\middleware\Check::class,
],
```

这样，所有该应用下的请求都会执行`Auth`和`Check`中间件。

### 使用闭包定义中间件

你不一定要使用中间件类，在某些简单的场合你可以使用闭包定义中间件，但闭包函数必须返回`Response`对象实例。

```php
Route::group('hello', function(){
    Route::rule('hello/:name','hello');
})->middleware(function($request,\Closure $next){
    if ($request->param('name') == 'think') {
        return redirect('index/think');
    }

    return $next($request);
});
```

### 控制器中间件

支持为控制器定义中间件，只需要在控制器中定义middleware属性，例如：

```php
<?php
namespace app\controller;

class Index
{
    protected $middleware = ['auth'];

    public function index()
    {
        return 'index';
    }

    public function hello()
    {
        return 'hello';
    }
}
```

当执行`index`控制器的时候就会调用`auth`中间件，一样支持使用完整的命名空间定义。

如果需要设置控制器中间的生效操作，可以如下定义：

```php
<?php
namespace app\controller;


class Index
{
    protected $middleware = [ 
        'auth'     => ['except'     => ['hello'] ],
        'check' => ['only'         => ['hello'] ],
    ];

    public function index()
    {
        return 'index';
    }

    public function hello()
    {
        return 'hello';
    }
}
```

### 注解中间件

定义好中间件后，在控制器中我们可以通过如下注解的方式精确到操作来调用中间件：
```php
<?php
namespace app\index\controller;

use app\middleware\User;
use think\annotation\route\Middleware;

class Index
{
    /**
     * index
     * @return mixed
     * @Route("index")
     * @Middleware({User::class})
     */
    public function index()
    {
        return "Hello TopPHP!";
    }
}
```

### 中间件向控制器传参

可以通过给请求对象赋值的方式传参给控制器（或者其它地方），例如

```php
<?php

namespace app\middleware;

class Hello
{
    public function handle($request, \Closure $next)
    {
        $request->hello = 'ThinkPHP';

        return $next($request);
    }
}
```

然后在控制器的方法里面可以直接使用

```php
public function index(Request $request)
{
    return $request->hello; // ThinkPHP
}
```

### 执行优先级

如果对中间件的执行顺序有严格的要求，可以定义中间件的执行优先级。在配置文件中添加

```php
return [
    'alias'    => [
        'check' => [
            app\middleware\Auth::class,
            app\middleware\Check::class,
        ],
    ],
    'priority' => [
        think\middleware\SessionInit::class,
        app\middleware\Auth::class,
        app\middleware\Check::class,
    ],
];
```

### 内置中间件

内置了几个系统中间件，包括：

| 中间件类 | 描述 |
| :--- | :--- |
| think\middleware\AllowCrossDomain | 跨域请求支持 |
| think\middleware\CheckRequestCache | 请求缓存 |
| think\middleware\LoadLangPack | 多语言加载 |
| think\middleware\SessionInit | Session初始化 |
| think\middleware\FormTokenCheck | 表单令牌 |
| app\middleware\Check | 验证器中间件 |

默认已经定义好验证器中间件，其他内置中间件默认都没有定义，你可以在应用的`middleware.php`文件中、路由或者控制器中定义这些中间件，如果不需要使用的话，取消定义即可。
