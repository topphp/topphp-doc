## 请求缓存

支持请求缓存功能，支持对GET请求设置缓存访问，并设置有效期。

> 请求缓存仅对GET请求有效

有两种方式可以设置请求缓存：

### 路由设置

可以在路由规则里面调用`cache`方法设置当前路由规则的请求缓存，例如：

```php
// 定义GET请求路由规则 并设置3600秒的缓存
Route::get('new/:id','News/read')->cache(3600);
```

第二次访问相同的路由地址的时候，会自动获取请求缓存的数据响应输出，并发送`304`状态码。

默认请求缓存的标识为当前访问的`pathinfo`地址，可以定义请求缓存的标识，如下：

```php
// 定义GET请求路由规则 并设置3600秒的缓存
Route::get('new/:id','News/read')->cache(
	[ 'new/:id/:page', 3600]
);
```

`:id`、`:page`表示使用当前请求的`param`参数进行动态标识替换，也就是根据`id`和`page`变量进行`3600`秒的请求缓存。

> 如果`cache`参数传入`false`，则表示关闭当前路由的请求缓存（即使开启全局请求缓存）。

```php
// 定义GET请求路由规则 并关闭请求缓存（即使开启了全局请求缓存）
Route::get('new/:id','News/read')->cache(false);
```

支持给一组路由设置缓存标签

```php
// 定义GET请求路由规则 并设置3600秒的缓存
Route::get('new/:id','News/read')->cache(
	[ 'new/:id/:page', 3600, 'page']
);
```

这样可以在需要的时候统一清理缓存标签为`page`的请求缓存。

## 全局请求缓存

如果需要开启全局请求缓存，只需要在全局（或者应用）的中间件定义文件`middleware.php`中增加

```
 'think\middleware\CheckRequestCache',
```

然后只需要在`route.php`配置文件中设置全局缓存的有效时间（秒）：

```php
'request_cache_expire'	=>	3600,
```

就会自动根据当前请求URL地址（只针对GET请求类型）进行请求缓存，全局缓存有效期为3600秒。

如果需要对全局缓存设置缓存规则，可以直接设置`request_cache_key`参数，例如：

```php
'request_cache_key'	=>	'__URL__',
'request_cache_expire'	=>	3600,
```

缓存标识支持下面的特殊定义

| 标识 | 含义 |
| :--- | :--- |
| `__CONTROLLER__` | 当前控制器名 |
| `__ACTION__` | 当前操作名 |
| `__URL__` | 当前完整URL地址（包含域名） |

全局请求缓存支持设置排除规则，使用方法如下：

```php
'request_cache_key'        => true,
'request_cache_expire' => 3600,
'request_cache_except' => [
    '/blog/index',
    '/user/member',
],
```

排除规则为不使用请求缓存的地址（不支持变量）开头部分（不区分大小写）。

> 路由中设置的请求缓存依然有效并且优先，如果需要设置特殊的请求缓存有效期就可以直接在路由中设置。



