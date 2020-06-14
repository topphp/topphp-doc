## 获取器

Db类也可以支持获取器定义，例如：

```php
Db::name('user')->withAttr('name', function($value, $data) {
	return strtolower($value);
})->select();
```

获取器方法支持传入两个参数，第一个参数是当前字段的值，第二个参数是所有的数据。

上面的代码，查询的数据集数据中的`name`字段的值会统一进行小写转换。

> `withAttr`方法可以多次调用，对多个字段定义获取器。

支持对`JSON`字段定义获取器，例如：

```php
$user = Db::name('user')
	->json(['info'])
    ->withAttr('info.name', function($value, $data) {
        return strtolower($value);
    })->find(1);
dump($user);    
```

查询结果返回的时候，会自动对`info`字段（`JSON`字段）的`name`属性使用获取器操作。

