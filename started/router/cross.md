## 跨域请求

如果某个路由或者分组需要支持跨域请求，可以使用

```php
Route::get('new/:id', 'News/read')
    ->ext('html')
    ->allowCrossDomain();
```

> 跨域请求一般会发送一条`OPTIONS`的请求，一旦设置了跨域请求的话，不需要自己定义`OPTIONS`请求的路由，系统会自动加上。

跨域请求系统会默认带上一些Header，包括：

```
Access-Control-Allow-Origin:*
Access-Control-Allow-Methods:GET, POST, PATCH, PUT, DELETE
Access-Control-Allow-Headers:Authorization, Content-Type, If-Match, If-Modified-Since, If-None-Match, If-Unmodified-Since, X-Requested-With

```

你可以添加或者更改Header信息，使用

```php
Route::get('new/:id', 'News/read')
    ->ext('html')
    ->allowCrossDomain([
        'Access-Control-Allow-Origin'        => 'thinkphp.cn',
        'Access-Control-Allow-Credentials'   => 'true'
    ]);
```



