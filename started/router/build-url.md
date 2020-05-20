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

## 使用路由地址

我们也可以直接使用路由地址来生成URL，例如：

我们定义了路由规则如下：

```
Route::get('blog/:id' , 'blog/read');
```

可以使用下面的方式直接使用路由规则生成URL地址：

```
Route::buildUrl('/blog/5');
```

那么自动生成的URL地址变为：

```
/index.php/blog/5.html

```

## URL后缀

默认情况下，系统会自动读取`url_html_suffix`配置参数作为URL后缀（默认为html），如果我们设置了：

```php
'url_html_suffix'   => 'shtml'
```

那么自动生成的URL地址变为：

```
/index.php/blog/5.shtml

```

如果我们设置了多个URL后缀支持

```php
'url_html_suffix'   => 'html|shtml'
```

则会取第一个后缀来生成URL地址，所以自动生成的URL地址还是：

```
/index.php/blog/5.html

```

如果你希望指定URL后缀生成，则可以使用：

```php
Route::buildUrl('blog/read', ['id'=>5])->suffix('shtml');
```

## 域名生成

默认生成的URL地址是不带域名的，如果你采用了多域名部署或者希望生成带有域名的URL地址的话，就需要传入第四个参数，该参数有两种用法：

### 自动生成域名

```php
Route::buildUrl('index/blog/read',  ['id'=>5])
    ->suffix('shtml')
    ->domain(true);
```

第四个参数传入`true`的话，表示自动生成域名，如果你开启了`url_domain_deploy`还会自动识别匹配当前URL规则的域名。

例如，我们注册了域名路由信息如下：

```php
Route::domain('blog','index/blog');
```

那么上面的URL地址生成为：

```
http://blog.thinkphp.cn/read/id/5.shtml

```

### 指定域名

你也可以显式传入需要生成地址的域名，例如：

```php
Route::buildUrl('blog/read', ['id'=>5])->domain('blog');
```

或者传入完整的域名

```php
Route::buildUrl('index/blog/read', ['id'=>5])->domain('blog.thinkphp.cn');
```

生成的URL地址为：

```
http://blog.thinkphp.cn/read/id/5.shtml

```

也可以直接在第一个参数里面传入域名，例如：

```php
Route::buildUrl('index/blog/read@blog',  ['id'=>5]);
Route::buildUrl('index/blog/read@blog.thinkphp.cn',  ['id'=>5]);
```

## 生成锚点

支持生成URL的锚点，可以直接在URL地址参数中使用：

```php
Route::buildUrl('index/blog/read#anchor@blog', ['id'=>5]);
```

> 锚点和域名一起使用的时候，注意锚点在前面，域名在后面。

生成的URL地址为：

```
http://blog.thinkphp.cn/read/id/5.html#anchor

```

## 加上入口文件

有时候我们生成的URL地址可能需要加上`index.php`或者去掉`index.php`，大多数时候系统会自动判断，如果发现自动生成的地址有问题，可以使用下面的方法：

```php
Route::buildUrl('index/blog/read', ['id'=>5])->root('/index.php');
```

## 助手函数

系统提供了一个`url`助手函数用于完成相同的功能，例如：

```php
url('index/blog/read', ['id'=>5])
    ->suffix('html')
    ->domain(true)
    ->root('/index.php');
```



