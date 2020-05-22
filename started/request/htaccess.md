URL伪静态通常是为了满足更好的SEO效果，ThinkPHP支持伪静态URL设置，可以通过设置`url_html_suffix`参数随意在URL的最后增加你想要的静态后缀，而不会影响当前操作的正常执行。例如，我们在`route.php`中设置

```php
'url_html_suffix' => 'shtml'
```

的话，我们可以把下面的URL

```
http://serverName/blog/read/id/1
```

变成

```
http://serverName/blog/read/id/1.shtml

```

后者更具有静态页面的URL特征，但是具有和前面的URL相同的执行效果，并且不会影响原来参数的使用。

默认情况下，伪静态的设置为`html`，如果我们设置伪静态后缀为空字符串，

```php
'url_html_suffix'=>''
```

则支持所有的静态后缀访问，如果要获取当前的伪静态后缀，可以使用`Request`对象的`ext`方法。

例如：

```php
http://serverName/blog/3.html
http://serverName/blog/3.shtml
http://serverName/blog/3.xml
http://serverName/blog/3.pdf

```

都可以正常访问。

我们可以在控制器的操作方法中获取当前访问的伪静态后缀，例如：

```php
$ext = Request::ext();
```

如果希望支持多个伪静态后缀，可以直接设置如下：

```php
// 多个伪静态后缀设置 用|分割
'url_html_suffix' => 'html|shtml|xml'
```

那么，当访问`http://serverName/blog/3.pdf`的时候会报系统错误。

如果要关闭伪静态访问，可以设置

```php
// 关闭伪静态后缀访问
'url_html_suffix' => false,
```

关闭伪静态访问后，不再支持伪静态方式的URL访问，并且伪静态后缀将会被解析为最后一个参数的值，例如：

```
http://serverName/blog/read/id/3.html
```

最终的id参数的值将会变成`3.html`。

