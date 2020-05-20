## 路由分组

路由分组功能允许把相同前缀的路由定义合并分组，这样可以简化路由定义，并且提高路由匹配的效率，不必每次都去遍历完整的路由规则（尤其是开启了路由延迟解析后性能更佳）。

使用`Route`类的`group`方法进行注册，给分组路由定义一些公用的路由设置参数，例如：

```php
Route::group('blog', function () {
    Route::rule(':id', 'blog/read');
    Route::rule(':name', 'blog/read');
})->ext('html')->pattern(['id' => '\d+', 'name' => '\w+']);
```

分组路由支持所有的路由参数设置，具体参数的用法请参考路由参数章节内容。

如果仅仅是用于对一些路由规则设置一些公共的路由参数（也称之为虚拟分组），也可以使用：

```php
Route::group(function () {
    Route::rule('blog/:id', 'blog/read');
    Route::rule('blog/:name', 'blog/read');
})->ext('html')->pattern(['id' => '\d+', 'name' => '\w+']);
```

路由分组支持嵌套，例如：

```php
Route::group(function () {
    Route::group('blog', function () {
        Route::rule(':id', 'blog/read');
        Route::rule(':name', 'blog/read');
    });
})->ext('html')->pattern(['id' => '\d+', 'name' => '\w+']);
```

> 如果使用了嵌套分组的情况，子分组会继承父分组的参数和变量规则，而最终的路由规则里面定义的参数和变量规则为最优先。

可以使用`prefix`方法简化相同路由地址的定义，例如下面的定义

```php
Route::group('blog', function () {
    Route::get(':id', 'blog/read');
    Route::post(':id', 'blog/update');
    Route::delete(':id', 'blog/delete');
})->ext('html')->pattern(['id' => '\d+']);
```

可以简化为

```php
Route::group('blog', function () {
    Route::get(':id', 'read');
    Route::post(':id', 'update');
    Route::delete(':id', 'delete');
})->prefix('blog/')->ext('html')->pattern(['id' => '\d+']);
```

## 路由完全匹配

如果希望某个分组下面的路由都采用完全匹配，可以使用

```php
Route::group('blog', function () {
    Route::get(':id', 'read');
    Route::post(':id', 'update');
    Route::delete(':id', 'delete');
})->completeMatch()->prefix('blog/')->ext('html')->pattern(['id' => '\d+']);
```

## 延迟路由解析

支持延迟路由解析，也就是说你定义的路由规则（主要是分组路由和域名路由规则）在加载路由定义文件的时候并没有实际注册，而是在匹配到路由分组或者域名的情况下，才会实际进行注册和解析，大大提高了路由注册和解析的性能。

默认是关闭延迟路由解析的，你可以在路由配置文件中设置：

```php
// 开启路由延迟解析
'url_lazy_route'         => true,
```

开启延迟路由解析后，如果你需要生成路由反解URL，需要使用命令行指令

```php
php think optimize:route

```

来生成路由缓存解析。

> 通过路由分组或者域名路由来定义路由才能发挥延迟解析的优势。

一旦开启路由的延迟解析，将会对定义的域名路由和分组路由进行延迟解析，也就是说只有实际匹配到该域名或者分组后才会进行路由规则的注册，避免不必要的注册和解析开销。

## 路由规则合并解析

同一个路由分组下的路由规则支持合并解析，而不需要遍历该路由分组下的所有路由规则，可以大大提升路由解析的性能。

对某个分组单独开启合并规则解析的用法如下：

```php
Route::group('user', function () {
    Route::rule('hello/:name','hello');
    Route::rule('think/:name','think');
})->mergeRuleRegex();
```

这样该分组下的所有路由规则无论定义多少个都只需要匹配检查一次即可（实际上只会合并检查符合当前请求类型的路由规则）。

> `mergeRuleRegex`方法只能用于路由分组或者域名路由（域名路由其实是一个特殊的分组）。

或者在路由配置文件中设置开启全局合并规则（对所有分组有效）

```php
// 开启路由合并解析
'route_rule_merge'	=> true,
```

## 传入额外参数

可以统一给分组路由传入额外的参数

```php
Route::group('blog', [
    ':id'   => 'Blog/read',
    ':name' => 'Blog/read',
])->ext('html')
->pattern(['id' => '\d+'])
->append(['group_id' => 1]);
```

上面的分组路由统一传入了`group_id`参数，该参数的值可以通过`Request`类的`param`方法获取。



