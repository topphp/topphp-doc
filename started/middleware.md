## 中间件

中间件主要用于拦截或过滤应用的HTTP请求，并进行必要的业务处理。

> ThinkPHP 6 部分核心功能使用中间件处理，你可以灵活关闭。包括Session功能、请求缓存和多语言功能。

**定义中间件**

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

**结束调度**

中间件支持定义请求结束前的回调机制，你只需要在中间件类中添加`end`方法。

```php
    public function end(\think\Response $response)
    {
        // 回调行为
    }
```

> 注意，在`end`方法里面不能有任何的响应输出。因为回调触发的时候请求响应输出已经完成了。

**前置/后置中间件**

中间件是在请求具体的操作之前还是之后执行，完全取决于中间件的定义本身。

下面是一个前置行为的中间件

```
<
```

```
?
php

namespace app\middleware
;


class 
Before
{

    public 
function
handle
(
$request
,
 \Closure $next
)
{
// 添加中间件执行代码
return
 $
next
(
$request
)
;
}
}
```

下面是一个后置行为的中间件

```
<
?
php

namespace app\middleware
;


class 
After
{

    public 
function
handle
(
$request
,
 \Closure $next
)
{

		$response 
=
 $
next
(
$request
)
;
// 添加中间件执行代码
return
 $response
;
}
}
```

中间件方法同样也可以支持依赖注入。

来个比较实际的例子，我们需要判断当前浏览器环境是在微信或支付宝

```
namespace app\middleware
;
/**
 * 访问环境检查，是否是微信或支付宝等
 */

class 
InAppCheck
{

    public 
function
handle
(
$request
,
 \Closure $next
)
{
if
(
preg_match
(
'~micromessenger~i'
,
 $request
-
>
header
(
'user-agent'
)
)
)
{

            $request
-
>
InApp 
=
'WeChat'
;
}
else
if
(
preg_match
(
'~alipay~i'
,
 $request
-
>
header
(
'user-agent'
)
)
)
{

            $request
-
>
InApp 
=
'Alipay'
;
}
return
 $
next
(
$request
)
;
}
}
```

然后在你的移动版的应用里添加一个`middleware.php`文件  
例如：`/path/app/mobile/middleware.php`

```
return
[

    app\middleware\InAppCheck
:
:
class
,
]
;
```

然后在你的`controller`中可以通过`request()->InApp`获取相关的值

## 定义中间件别名

可以直接在应用配置目录下的`middleware.php`中先预定义中间件（其实就是增加别名标识），例如：

```
return
[
'alias'
=
>
[
'auth'
=
>
 app\middleware\Auth
:
:
class
,
'check'
=
>
 app\middleware\Check
:
:
class
,
]
,
]
;
```

可以支持使用别名定义一组中间件，例如：

```
return
[
'alias'
=
>
[
'check'
=
>
[

            app\middleware\Auth
:
:
class
,

            app\middleware\Check
:
:
class
,
]
,
]
,
]
;
```

## 注册中间件

新版的中间件分为全局中间件、应用中间件（多应用模式下有效）、路由中间件以及控制器中间件四个组。执行顺序分别为：

> 全局中间件-&gt;应用中间件-&gt;路由中间件-&gt;控制器中间件

### 全局中间件

全局中间件在`app`目录下面`middleware.php`文件中定义，使用下面的方式：

```
<
?
php


return
[

	\app\middleware\Auth
:
:
class
,
'check'
,
'Hello'
,
]
;
```

中间件的注册应该使用完整的类名，如果已经定义了中间件别名（或者分组）则可以直接使用。

全局中间件的执行顺序就是定义顺序。可以在定义全局中间件的时候传入中间件参数，支持两种方式传入。

```
<
?
php


return
[
[
\app\http\middleware\Auth
:
:
class
,
'admin'
]
,
'Check'
,
[
'hello'
,
'thinkphp'
]
,
]
;
```

上面的定义表示 给`Auth`中间件传入`admin`参数，给`Hello`中间件传入`thinkphp`参数。

