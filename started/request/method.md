## 获取请求类型

在很多情况下面，我们需要判断当前操作的请求类型是`GET`、`POST`、`PUT`、`DELETE`或者`HEAD`，一方面可以针对请求类型作出不同的逻辑处理，另外一方面有些情况下面需要验证安全性，过滤不安全的请求。

请求对象`Request`类提供了下列方法来获取或判断当前请求类型：

| 用途 | 方法 |
| :--- | :--- |
| 获取当前请求类型 | method |
| 判断是否GET请求 | isGet |
| 判断是否POST请求 | isPost |
| 判断是否PUT请求 | isPut |
| 判断是否DELETE请求 | isDelete |
| 判断是否AJAX请求 | isAjax |
| 判断是否PJAX请求 | isPjax |
| 判断是否JSON请求 | isJson |
| 判断是否手机访问 | isMobile |
| 判断是否HEAD请求 | isHead |
| 判断是否PATCH请求 | isPatch |
| 判断是否OPTIONS请求 | isOptions |
| 判断是否为CLI执行 | isCli |
| 判断是否为CGI模式 | isCgi |

> `method`方法返回的请求类型始终是大写，这些方法都不需要传入任何参数。

> 没有必要在控制器中判断请求类型再来执行不同的逻辑，完全可以在路由中进行设置。

## 请求类型伪装

支持请求类型伪装，可以在`POST`表单里面提交`_method`变量，传入需要伪装的请求类型，例如：

```php
<form method="post" action="">
    <input type="text" name="name" value="Hello">
    <input type="hidden" name="_method" value="PUT" >
    <input type="submit" value="提交">
</form>
```

提交后的请求类型会被系统识别为`PUT`请求。

> 你可以设置为任何合法的请求类型，包括`GET`、`POST`、`PUT`和`DELETE`等，但伪装变量`_method`只能通过POST请求进行提交。

如果要获取原始的请求类型，可以使用

```php
Request::method(true);
```

> 在命令行下面执行的话，请求类型返回的始终是`GET`。

如果你需要改变伪装请求的变量名，可以修改自定义Request类的`varMethod`属性：

## `AJAX/PJAX`伪装

可以对请求进行`AJAX`请求伪装，如下：

```php
http://localhost/index?_ajax=1
```

或者`PJAX`请求伪装

```php
http://localhost/index?_pjax=1
```

如果你需要改变伪装请求的变量名，可以修改自定义Request类的`varAjax`和`varPjax`属性：

> `_ajax`和`_pjax`可以通过`GET/POST/PUT`等请求变量伪装。



