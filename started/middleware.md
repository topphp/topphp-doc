##中间件

中间件主要用于拦截或过滤应用的HTTP请求，并进行必要的业务处理。

>ThinkPHP 6 部分核心功能使用中间件处理，你可以灵活关闭。包括Session功能、请求缓存和多语言功能。

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

>中间件`handle`方法的返回值必须是一个`Response`对象，推荐使用TopPHP提供的`SendMsg`类进行返回。

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

>在某些需求下，可以使用第三个参数传入额外的参数。

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
>注意，在`end`方法里面不能有任何的响应输出。因为回调触发的时候请求响应输出已经完成了。

**前置/后置中间件**

中间件是在请求具体的操作之前还是之后执行，完全取决于中间件的定义本身。

下面是一个前置行为的中间件


