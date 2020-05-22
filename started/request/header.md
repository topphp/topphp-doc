## header 头

可以使用`Request`对象的`header`方法获取当前请求的`HTTP`请求头信息，例如：

```php
$info = Request::header();
echo $info['accept'];
echo $info['accept-encoding'];
echo $info['user-agent'];
```

也可以直接获取某个请求头信息，例如：

```php
$agent = Request::header('user-agent');
```

`HTTP`请求头信息的名称不区分大小写，并且`_`会自动转换为`-`，所以下面的写法都是等效的：

```php
$agent = Request::header('user-agent');
$agent = Request::header('USER_AGENT');
```



