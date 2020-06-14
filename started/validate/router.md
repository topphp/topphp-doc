## 路由验证

可以在路由规则定义的时候调用`validate`方法指定验证器类对请求的数据进行验证。

例如下面的例子表示对请求数据使用验证器类`app\validate\User`进行自动验证，并且使用`edit`验证场景：

```php
Route::post('hello/:id', 'index/hello')
    ->validate(\app\validate\User::class,'edit');
```

或者不使用验证器而直接传入验证规则

```php
Route::post('hello/:id', 'index/hello')
    ->validate([
        'name' => 'min:5|max:50',
        'email' => 'email',
    ]);
```

也支持使用对象化规则定义

```php
Route::post('hello/:id', 'index/hello')
    ->validate([
        'name' => ValidateRule::min(5)->max(50),
        'email' => ValidateRule::isEmail(),
    ]);
```



