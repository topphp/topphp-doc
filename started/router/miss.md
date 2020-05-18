## 全局MISS路由

如果希望在没有匹配到所有的路由规则后执行一条设定的路由，可以注册一个单独的`MISS`路由：

```
Route::miss('public/miss');
```

复制

或者使用闭包定义

```
Route::miss(function() {
    return '404 Not Found!';
});
```

> 一旦设置了MISS路由，相当于开启了强制路由模式

当所有已经定义的路由规则都不匹配的话，会路由到`miss`方法定义的路由地址，并且swoole服务下不会区分应用，在任何应用下配置MISS路由都会全局触发。

如果需要多应用配置不同的MISS路由，请使用分组MISS路由功能。

你可以限制`MISS`路由的请求类型

```
// 只有GET请求下MISS路由有效

Route::miss('public/miss', 'get');
```

## 分组MISS路由

分组支持独立的`MISS`路由，例如如下定义：

```
Route::group('blog', function () {
    Route::rule(':id', 'blog/read');
    Route::rule(':name', 'blog/read');
    Route::miss('blog/miss');
})->ext('html')
  ->pattern(['id' => '\d+', 'name' => '\w+']);
```

多应用下分组支持独立的`MISS`路由，例如如下多版本API路由定义：

```
// api 版本路由【以下路由配置在api应用route文件夹下的route.php中，且路由顺序不可更改】
// 请求URL实例：http://domain/api/v1/控制器/方法名
Route::group('v1', function () {
    Route::rule(':controller/:action', 'v1.:controller/:action', '*')
        ->pattern(['controller' => '\w+', 'action' => '\w+']);
    Route::rule(':controller', 'v1.:controller/index', '*');
    Route::rule('', 'v1.Index/index', '*');
    Route::miss(function () {
        if (request()->isAjax() || request()->isPjax()) {
            return \lib\SendMsg::jsonAlert('404 Not Found!', CommonCodeEnum::FAIL, [], 404);
        } else {
            return response('404 Not Found!', 404);
        }
    });
});
// 请求URL实例：http://domain/api/v2/控制器/方法名
Route::group('v2', function () {
    Route::rule(':controller/:action', 'v2.:controller/:action', '*')
        ->pattern(['controller' => '\w+', 'action' => '\w+']);
    Route::rule(':controller', 'v2.:controller/index', '*');
    Route::rule('', 'v2.Index/index', '*');
    Route::miss(function () {
        if (request()->isAjax() || request()->isPjax()) {
            return \lib\SendMsg::jsonAlert('404 Not Found!', CommonCodeEnum::FAIL, [], 404);
        } else {
            return response('404 Not Found!', 404);
        }
    });
});
```

## 域名MISS路由

支持给某个域名设置单独的`MISS`路由

```
Route::domain('blog', function () {
    // 动态注册域名的路由规则
    Route::rule('new/:id', 'news/read');
    Route::rule(':user', 'user/info');
    Route::miss('blog/miss');
});
```