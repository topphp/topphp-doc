## 路由中间件

可以使用路由中间件，注册方式如下：

```php
Route::rule('hello/:name','hello')
	->middleware(\app\middleware\Auth::class);
```

或者对路由分组注册中间件

```php
Route::group('hello', function(){
	Route::rule('hello/:name','hello');
})->middleware(\app\middleware\Auth::class);
```

如果需要传入额外参数给中间件，可以使用

```php
Route::rule('hello/:name','hello')
	->middleware(\app\middleware\Auth::class,'admin');
```

如果需要定义多个中间件，使用数组方式

```php
Route::rule('hello/:name','hello')
	->middleware([\app\middleware\Auth::class,\app\middleware\Check::class]);
```

可以统一传入同一个额外参数

```php
Route::rule('hello/:name','hello')
	->middleware([\app\middleware\Auth::class, \app\middleware\Check::class], 'admin');
```

如果你希望某个路由中间件是全局执行（不管路由是否匹配），可以不需要在路由里面定义，支持直接在路由配置文件中定义，例如在`config/route.php`配置文件中添加：

```php
'middleware'    =>    [
    app\middleware\Auth::class,
    app\middleware\Check::class,
],
```

这样，所有该应用下的请求都会执行`Auth`和`Check`中间件。



