## 响应参数

`Response`对象提供了一系列方法用于设置响应参数，包括设置输出内容、状态码及`header`信息等，并且支持链式调用以及多次调用。

## 设置数据

`Response`基类提供了`data`方法用于设置响应数据。

```php
response()->data($data);
json()->data($data);
```

不过需要注意的是`data`方法设置的只是原始数据，并不一定是最终的输出数据，最终的响应输出数据是会根据当前的`Response`响应类型做自动转换的，例如：

```php
json()->data($data);
```

最终的输出数据就是`json_encode($data)`转换后的数据。

> 如果要获取当前响应对象实例的实际输出数据可以使用`getContent`方法。

## 设置状态码

`Response`基类提供了`code`方法用于设置响应数据，但大部分情况一般我们是直接在调用助手函数的时候直接传入状态码，例如：

```php
json($data,201);
view($data,401);
```

或者在后面链式调用`code`方法是等效的：

```php
json($data)->code(201);
```

> 除了`redirect`函数的默认返回状态码是`302`之外，其它方法没有指定状态码都是返回`200`状态码。

如果要获取当前响应对象实例的状态码的值，可以使用`getCode`方法。

## 设置头信息

可以使用`Response`类的`header`设置响应的头信息

```php
json($data)->code(201)->header([
    'Cache-control' => 'no-cache,must-revalidate'
]);
```

除了`header`方法之外，`Response`基类还提供了常用头信息的快捷设置方法：

| 方法名 | 作用 |
| :--- | :--- |
| `lastModified` | 设置`Last-Modified`头信息 |
| `expires` | 设置`Expires`头信息 |
| `eTag` | 设置`ETag`头信息 |
| `cacheControl` | 设置`Cache-control`头信息 |
| `contentType` | 设置`Content-Type`头信息 |

除非你要清楚自己在做什么，否则不要随便更改这些头信息，每个`Response`子类都有默认的`contentType`信息，一般无需设置。

你可以使用`getHeader`方法获取当前响应对象实例的头信息。

## 写入Cookie

```php
response()->cookie('name', 'value', 600);
```

## 设置额外参数

有些时候，响应输出需要设置一些额外的参数，例如：  
在进行`json`输出的时候需要设置`json_encode`方法的额外参数，`jsonp`输出的时候需要设置`jsonp_handler`等参数，这些都可以使用`options`方法来进行处理，例如：

```php
jsonp($data)->options([
    'var_jsonp_handler'     => 'callback',
    'default_jsonp_handler' => 'jsonpReturn',
    'json_encode_param'     => JSON_PRETTY_PRINT,
]);
```

## 关闭当前的请求缓存

支持使用`allowCache`方法动态控制是否需要使用请求缓存。

```php
// 关闭当前页面的请求缓存
json($data)->code(201)->allowCache(false);
```

## 自定义响应

如果需要特别的自定义响应输出，可以自定义一个`Response`子类，并且在控制器的操作方法中直接返回。又或者通过设置响应参数的方式进行响应设置输出。



