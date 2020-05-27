## Code 响应

通常情况下，我们开发一个前后端分离的项目，都需要自己定义一套响应数据的结构，而最常见的跨语言数据结构就是json了。

`TopPHP`考虑到这种需求的广泛度，骨架提供了`SendMsg`类来规范`Code`响应规则，通常也是最常见的规则。

```js
// 操作成功的响应示例
{"code":10000,"message":"success","data":{"id":"1"}}

// 操作失败的响应示例
{"code":40000,"message":"fail","data":[]}
```

#### 返回数据--jsonData

> SendMsg::jsonData\("数据内容（支持数组）","http状态码（默认200）", "header信息（数组）"\);

通常我们需要向客户端响应一个数据内容时（例如展示用户信息，展示一条数据库数据的编辑信息等），我们可以在控制器中使用这个方法。

```php
declare(strict_types=1);

namespace app\index\controller;

use lib\SendMsg;

class User extends Base
{
    public function index()
    {
        $data = [
            "id"       => 1,
            "username" => "zhangsan",
            "email"    => "abc@domain.com",
            "phone"    => "186****1234"
        ];
        return SendMsg::jsonData($data);
    }
}
```

访问`index/User/index`我们将得到如下结果：

```json
{
    "code":10000,
    "message":"success",
    "data":{
        "id":1,
        "username":"zhangsan",
        "email":"abc@domain.com",
        "phone":"186****1234"
    },
    "operate":"index/User/index"
}
```

其中的`operate`参数仅在应用开启了`DEBUG`模式时返回，标识当前访问的操作。`jsonData`固定返回`code`码为 `10000`，通常用于返回给客户端接口请求成功的状态。

我们还可以在`app\common\enumerate\CommonCodeEnum.php`枚举文件中自定义返回成功的状态码为多少，属性类型为`int`型。不推荐设置为`0`或`1`，建议保留`TopPHP`默认的`10000`成功，`40000`失败。

> 可以通过配置文件`config\app.php`中的`show_http_status`参数来控制是否在响应数据中加入显示`httpStatus`。默认是`false`不显示的，如果为`true`，上面的例子将会返回如下结构：

```json
{
    "code":10000,
    "message":"success",
    "data":{
        "id":1,
        "username":"zhangsan",
        "email":"abc@domain.com",
        "phone":"186****1234"
    },
    "StatusCode":200,
    "operate":"index/User/index"
}
```

> 无论是否开启`show_http_status`参数，都不影响我们实际响应给客户端的`http`状态。我们可以通过`jsonData`的第二个参数来自定义响应给客户端的`http`状态。

```php
return SendMsg::jsonData($data, 500);
```

上面的例子将会返回如下效果：

```json
{
    "code":10000,
    "message":"success",
    "data":{
        "id":1,
        "username":"zhangsan",
        "email":"abc@domain.com",
        "phone":"186****1234"
    },
    "StatusCode":500,
    "operate":"index/User/index"
}
```

如果是浏览器访问的话，我们会在`Network`的`headers`下看到如下响应：

![code 500](/assets/500.png)

> `jsonData`同样支持直接在`data`参数返回字符串的情况：

```php
$data = "操作成功";
return SendMsg::jsonData($data);
```

```json
{
    "code":10000,
    "message":"success",
    "data":"操作成功",
    "StatusCode":200,
    "operate":"index/User/index"
}
```

如果你需要将返回的信息传递给`message`参数，那么我们可以使用`jsonAlert`方法

#### 返回弹层/提示/消息/警告等信息--jsonAlert

> SendMsg::jsonAlert\("错误信息", "code码", "附加数据（支持数组）", "http状态码（默认200）", "header信息（数组）"\);

```php
return SendMsg::jsonAlert("操作成功");
```

`jsonAlert`方法应用场景常用于给客户端一个提示消息，或者警告消息等，故默认的`code`返回值是`40000`，`http`状态码默认`200`。上面的例子将会返回如下结果：

```json
{
    "code":40000,
    "message":"操作成功",
    "data":[

    ],
    "operate":"index/User/index"
}
```

显然我们需要的是一个成功的提示，而不是一个异常的提示，所以我们可以通过如下方式定义：

```php
return SendMsg::jsonAlert("操作成功", 10000);
// 或者通过枚举类定义
return SendMsg::jsonAlert("操作成功", CommonCodeEnum::SUCCESS);
```

得到结果：

```json
{
    "code":10000,
    "message":"操作成功",
    "data":[

    ],
    "operate":"index/User/index"
}
```

通常情况下，前端不需要根据返回信息去判断是否成功，而是通过code码来判断成功与否，所以如果不是特殊的业务需求，建议操作成功的返回使用`jsonData`方法。

为了满足需要后端来控制成功的提示语的这种需求，所以`SendMsg`提供像上面这种定义方式来实现。而`jsonAlert`方法主要的作用场景依旧是`错误`或`异常`或是`操作失败`的提示。

如果我们需要在提示错误的信息同时，还需要返回一些详细的错误内容，可以传入第三个参数，支持传入`数组`或`字符串`：

```php
$data = [
    "status" => 0,
    "error"  => "调用第三方api接口异常"
];
return SendMsg::jsonAlert("操作失败", CommonCodeEnum::FAIL, $data);
```

得到结果：

```json
{
    "code":40000,
    "message":"操作失败",
    "data":{
        "status":0,
        "error":"调用第三方api接口异常"
    },
    "operate":"index/User/index"
}
```

我们还可以自定义`http`状态码和`header`内容：

```php
$data    = [
    "status" => 0,
    "error"  => "调用第三方api接口异常"
];
$headers = [
    "Cache-Control" => "no-cache, must-revalidate",
    "Pragma"        => "no-cache"
];
return SendMsg::jsonAlert("操作失败", CommonCodeEnum::FAIL, $data, HttpStatusEnum::SERVER_ERROR, $headers);
```

![code header](/assets/header.png)

> `TopPHP`提供`http`状态枚举类`HttpStatusEnum`。上面的`SERVER_ERROR`即是`500`。另外提供如下常用`http`状态码枚举：

| 静态调用 | http状态码 | 含义 |
| :--- | :--- | :--- |
| HttpStatusEnum::SUCCESS | 200 | 访问成功 |
| HttpStatusEnum::NO\_CONTENT | 204 | 接口调用成功，但无返回内容 |
| HttpStatusEnum::REDIRECT | 302 | 临时重定向 |
| HttpStatusEnum::SYSTEM\_INVALID | 400 | 系统参数错误 |
| HttpStatusEnum::PARAMS\_ERROR | 401 | 接口参数错误 |
| HttpStatusEnum::FORBIDDEN | 403 | 禁止访问（黑名单） |
| HttpStatusEnum::NOT\_FOUND | 404 | 页面不存在 |
| HttpStatusEnum::VALIDATE\_ERROR | 422 | 参数验证错误 |
| HttpStatusEnum::SERVER\_ERROR | 500 | 服务器异常 |

> 关于重定向，我们可以参考[重定向](/started/response/redirect.md)章节的`jsonJump`方式进行重定向的处理。

#### 返回List（数据列表形式）--jsonList

> SendMsg::jsonList\("列表内容（数组）","http状态码（默认200）", "header信息（数组）"\);



