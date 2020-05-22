可以通过`Request`对象完成全局输入变量的检测、获取和安全过滤，支持包括`$_GET`、`$_POST`、`$_REQUEST`、`$_SERVER`、`$_SESSION`、`$_COOKIE`、`$_ENV`等系统变量，以及文件上传信息。

为了方便说明，本篇内容的所有示例代码均使用`Facade`方式，因此需要首先引入

```php
use think\facade\Request;
```

如果你使用的是依赖注入，请自行调整代码为动态调用即可。

主要内容包括：

* [检测变量是否设置](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_14)
* [变量获取](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_25)
* [默认值](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_88)
* [变量过滤](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_100)
* [获取部分变量](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_141)
* [变量修饰符](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_190)
* [中间件变量](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_216)
* [助手函数](https://www.kancloud.cn/manual/thinkphp6_0/1037519#_238)

## 检测变量是否设置

可以使用`has`方法来检测一个变量参数是否设置，如下：

```php
Request::has('id','get');
Request::has('name','post');
```

变量检测可以支持所有支持的系统变量，包括`get/post/put/request/cookie/server/session/env/file`。

## 变量获取

变量获取使用`\think\Request`类的如下方法及参数：

> ### 变量类型方法\('变量名/变量修饰符','默认值','过滤方法'\)

变量类型方法包括：

| 方法 | 描述 |
| :--- | :--- |
| param | 获取当前请求的变量 |
| get | 获取 $\_GET 变量 |
| post | 获取 $\_POST 变量 |
| put | 获取 PUT 变量 |
| delete | 获取 DELETE 变量 |
| session | 获取 SESSION 变量 |
| cookie | 获取 $\_COOKIE 变量 |
| request | 获取 $\_REQUEST 变量 |
| server | 获取 $\_SERVER 变量 |
| env | 获取 $\_ENV 变量 |
| route | 获取 路由（包括PATHINFO） 变量 |
| middleware | 获取 中间件赋值/传递的变量 |
| file | 获取 $\_FILES 变量 |

### 获取`PARAM`变量

`PARAM`类型变量是框架提供的用于自动识别当前请求的一种变量获取方式，是系统推荐的获取请求参数的方法，用法如下：

```php
// 获取当前请求的name变量
Request::param('name');
// 获取当前请求的所有变量（经过过滤）
Request::param();
// 获取当前请求未经过滤的所有变量
Request::param(false);
// 获取部分变量
Request::param(['name', 'email']);
```

> `param`方法会把当前请求类型的参数和路由变量以及GET请求合并，并且路由变量是优先的。

其它的输入变量获取方法和`param`方法用法基本一致。

你无法使用**get方法获取路由变量**，例如当访问地址是

```
http://localhost/index.php/index/index/hello/name/thinkphp

```

下面的用法是错误的

```php
echo Request::get('name'); // 输出为空
```

正确的用法是

```php
echo Request::param('name'); // 输出thinkphp
```

> 除了`server`和`env`方法的变量名不区分大小写（会自动转为大写后获取），其它变量名区分大小写。

## 默认值

获取输入变量的时候，可以支持默认值，例如当URL中不包含`$_GET['name']`的时候，使用下面的方式输出的结果比较。

```php
Request::get('name'); // 返回值为null
Request::get('name',''); // 返回值为空字符串
Request::get('name','default'); // 返回值为default
```

前面提到的方法都支持在第二个参数中传入默认值的方式。

## 变量过滤

> 框架默认没有设置任何全局过滤规则，你可以在`app\Request`对象中设置`filter`全局过滤属性：

```php
namespace app;

class Request extends \think\Request
{
    protected $filter = ['htmlspecialchars'];
}
```

也支持使用`Request`对象进行全局变量的获取过滤，过滤方式包括函数、方法过滤，以及PHP内置的Types of filters，我们可以设置全局变量过滤方法，支持设置多个过滤方法，例如：

```php
Request::filter(['strip_tags','htmlspecialchars'])
```

也可以在获取变量的时候添加过滤方法，例如：

```php
Request::get('name','','htmlspecialchars'); // 获取get变量 并用htmlspecialchars函数过滤
Request::param('username','','strip_tags'); // 获取param变量 并用strip_tags函数过滤
Request::post('name','','org\Filter::safeHtml'); // 获取post变量 并用org\Filter类的safeHtml方法过滤
```

可以支持传入多个过滤规则，例如：

```php
// 获取param变量 并依次调用strip_tags、strtolower函数过滤
Request::param('username','','strip_tags,strtolower');
```

如果当前不需要进行任何过滤的话，可以使用

```php
// 获取get变量 并且不进行任何过滤 即使设置了全局过滤
Request::get('name', '', null);
```

> 对于body中提交的`json`对象，你无需使用`php://input`去获取，可以直接当做表单提交的数据使用，因为系统已经自动处理过了

## 获取部分变量

如果你只需要获取当前请求的部分参数，可以使用：

```php
// 只获取当前请求的id和name变量
Request::only(['id','name']);
```

> 采用`only`方法能够安全的获取你需要的变量，避免额外变量影响数据处理和写入。

`only`方法可以支持批量设置默认值，如下：

```php
// 设置默认值
Request::only(['id'=>0,'name'=>'']);
```

表示`id`的默认值为0，`name`的默认值为空字符串。

默认获取的是当前请求参数（`PARAM`类型变量），如果需要获取其它类型的参数，可以在第二个参数传入，例如：

```php
// 只获取GET请求的id和name变量
Request::only(['id','name'], 'get');
// 等效于
Request::get(['id', 'name']);
// 只获取POST请求的id和name变量
Request::only(['id','name'], 'post');
// 等效于
Request::post(['id', 'name']);
```

也支持排除某些变量后获取，例如

```php
// 排除id和name变量
Request::except(['id','name']);
```

同样支持指定变量类型获取：

```php
// 排除GET请求的id和name变量
Request::except(['id','name'], 'get');
// 排除POST请求的id和name变量
Request::except(['id','name'], 'post');
```

## 变量修饰符

支持对变量使用修饰符功能，可以一定程度上简单过滤变量，更为严格的过滤请使用前面提过的变量过滤功能。

用法如下：

> ### Request::变量类型\('变量名/修饰符'\);

支持的变量修饰符，包括：

| 修饰符 | 作用 |
| :--- | :--- |
| s | 强制转换为字符串类型 |
| d | 强制转换为整型类型 |
| b | 强制转换为布尔类型 |
| a | 强制转换为数组类型 |
| f | 强制转换为浮点类型 |

下面是一些例子：

```php
Request::get('id/d');
Request::post('name/s');
Request::post('ids/a');
```

## 中间件变量

可以在中间件里面设置和获取请求变量的值，这个值的改变不会影响`PARAM`变量的获取。

```php
<?php

namespace app\http\middleware;

class Check
{
    public function handle($request, \Closure $next)
    {
        if ('think' == $request->name) {
        	$request->name = 'ThinkPHP';
        }

        return $next($request);
    }
}
```

## 助手函数

为了简化使用，还可以使用系统提供的`input`助手函数完成上述大部分功能。

判断变量是否定义

```php
input('?get.id');
input('?post.name');
```

获取PARAM参数

```php
input('param.name'); // 获取单个参数
input('param.'); // 获取全部参数
// 下面是等效的
input('name'); 
input('');
```

获取GET参数

```php
// 获取单个变量
input('get.id');
// 使用过滤方法获取 默认为空字符串
input('get.name');
// 获取全部变量
input('get.');
```

使用过滤方法

```php
input('get.name','','htmlspecialchars'); // 获取get变量 并用htmlspecialchars函数过滤
input('username','','strip_tags'); // 获取param变量 并用strip_tags函数过滤
input('post.name','','org\Filter::safeHtml'); // 获取post变量 并用org\Filter类的safeHtml方法过滤
```

使用变量修饰符

```php
input('get.id/d');
input('post.name/s');
input('post.ids/a');
```



