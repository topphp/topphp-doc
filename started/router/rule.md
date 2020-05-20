## 变量规则

>系统默认的变量规则设置是`\w+`，只会匹配字母、数字、中文和下划线字符，并不会**匹配特殊符号以及其它字符**，需要定义变量规则或者调整默认变量规则。

可以在路由配置文件中自定义默认的变量规则，例如增加中划线字符的匹配：

```php
'default_route_pattern'	=>	'[\w\-]+',
```

支持在规则路由中指定变量规则，弥补了动态变量无法限制具体的类型问题，并且支持全局规则设置。使用方式如下：

### 局部变量规则

局部变量规则，仅在当前路由有效：

```php
// 定义GET请求路由规则 并设置name变量规则
Route::get('new/:name', 'News/read')
    ->pattern(['name' => '[\w|\-]+']);
```

> 不需要开头添加`^`或者在最后添加`$`，也不支持模式修饰符，系统会自动添加。

### 全局变量规则

设置全局变量规则，全部路由有效：

```php
// 支持批量添加
Route::pattern([
    'name' => '\w+',
    'id'   => '\d+',
]);
```

## 组合变量

如果你的路由规则比较特殊，可以在路由定义的时候使用组合变量。

例如：

```php
Route::get('item-<name>-<id>', 'product/detail')
    ->pattern(['name' => '\w+', 'id' => '\d+']);
```

组合变量的优势是路由规则中没有固定的分隔符，可以随意组合需要的变量规则和分割符，例如路由规则改成如下一样可以支持：

```php
Route::get('item<name><id>', 'product/detail')
    ->pattern(['name' => '[a-zA-Z]+', 'id' => '\d+']);
Route::get('item@<name>-<id>', 'product/detail')
    ->pattern(['name' => '\w+', 'id' => '\d+']);
```

使用组合变量的情况下如果需要使用可选变量，则可以使用下面的方式：

```php
Route::get('item-<name><id?>', 'product/detail')
    ->pattern(['name' => '[a-zA-Z]+', 'id' => '\d+']);
```

## 动态路由

可以把路由规则中的变量传入路由地址中，就可以实现一个动态路由，例如：

```php
// 定义动态路由
Route::get('hello/:name', 'index/:name/hello');
```

`name`变量的值作为路由地址传入。

动态路由中的变量也支持组合变量及拼装，例如：

```php
Route::get('item-<name>-<id>', 'product_:name/detail')
    ->pattern(['name' => '\w+', 'id' => '\d+']);
```



