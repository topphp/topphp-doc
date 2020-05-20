## 域名路由

ThinkPHP支持完整域名、子域名和IP部署的路由和绑定功能，同时还可以起到简化URL的作用。

可以单独给域名设置路由规则，例如给`blog`子域名注册单独的路由规则：

```php
Route::domain('blog', function () {
    // 动态注册域名的路由规则
    Route::rule('new/:id', 'news/read');
    Route::rule(':user', 'user/info');
});
```

一旦定义了域名路由，该域名的访问就只会读取域名路由定义的路由规则。

> 闭包中可以使用路由的其它方法，包括路由分组，但不能再包含域名路由

支持同时对多个域名设置相同的路由规则：

```php
Route::domain(['blog', 'admin'], function () {
    // 动态注册域名的路由规则
    Route::rule('new/:id', 'news/read');
    Route::rule(':user', 'user/info');
});
```

如果你需要设置一个路由跨所有域名都可以生效，可以对分组路由或者某个路由使用`crossDomainRule`方法设置：

```php
Route::group( function () {
    // 动态注册域名的路由规则
    Route::rule('new/:id', 'news/read');
    Route::rule(':user', 'user/info');
})->crossDomainRule();
```

## 域名绑定

### 绑定到控制器类

```php
// blog子域名绑定控制器
Route::domain('blog', '@blog');
```

### 绑定到命名空间

```php
// blog子域名绑定命名空间
Route::domain('blog', ':\app\blog\controller');
```

### 绑定到类

```php
// blog子域名绑定到类
Route::domain('blog', '\app\blog\controller\Article');
```

### 绑定到Response对象

可以直接绑定某个域名到`Response`对象，例如：

```php
// 绑定域名到Response对象
Route::domain('test', response()->code(404));
```

如果域名需要同时定义路由规则，并且对其它的情况进行绑定操作，可以在闭包里面执行绑定操作，例如：

```php
Route::domain('blog', function () {
    // 动态注册域名的路由规则
    Route::rule('new/:id', 'index/news/read');
})->bind('blog');
```

在`blog`域名下面定义了一个`new/:id`的路由规则，指向`index`应用，而其它的路由则绑定到`blog`应用。

### 路由参数

域名路由本身也是一个路由分组，所以可以和路由分组一样定义公共的路由参数，例如：

```php
Route::domain('blog', function () {
    // 动态注册域名的路由规则
    Route::rule('new/:id', 'news/read');
    Route::rule(':user', 'user/info');
})->ext('html')
->pattern(['id' => '\d+'])
->append(['group_id' => 1]);
```



