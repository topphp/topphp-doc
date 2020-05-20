## URL生成

ThinkPHP支持路由URL地址的统一生成，并且支持所有的路由方式，以及完美解决了路由地址的反转解析，无需再为路由定义和变化而改变URL生成。

> 如果你开启了路由延迟解析，需要生成路由映射缓存才能支持全部的路由地址的反转解析。

URL生成使用`\think\facade\Route::buildUrl()`方法即可。该方法会返回一个`think\route\Url`对象实例，因为使用了`__toString`方法，因此可以直接输出路由地址。

```
echo \think\facade\Route::buildUrl();
```

如果是通过数据返回客户端，你可以先强制转换为字符串类型后再返回。

```
$url = (string) \think\facade\Route::buildUrl();
```

## 使用路由标识

对使用不同的路由地址方式，地址表达式的定义有所区别。参数单独通过第二个参数传入，假设我们定义了一个路由规则如下：

```php
Route::rule('blog/:id','blog/read');
```

在没有指定路由标识的情况下，可以直接使用路由地址来生成URL地址：

```php
Route::buildUrl('blog/read', ['id' => 5, 'name' => 'thinkphp']);
```

如果我们在注册路由的时候指定了路由标识

```php
Route::rule('blog/:id','blog/read')->name('blog_read');
```

那么必须使用路由标识来生成URL地址

```php
Route::buildUrl('blog_read', ['id' => 5, 'name' => 'thinkphp']);
```

以上方法都会生成下面的URL地址：

```
/index.php/blog/5/name/thinkphp.html

```

如果你的环境支持REWRITE，那么生成的URL地址会变为：

```
/blog/5/name/thinkphp.html

```

如果你配置了：

```php
'url_common_param'=>true
```

那么生成的URL地址变为：

```
/index.php/blog/5.html?name=thinkphp

```

不在路由规则里面的变量会直接使用普通URL参数的方式。

> 需要注意的是，URL地址生成不会检测路由的有效性，只是按照给定的路由地址和参数生成符合条件的路由规则。



