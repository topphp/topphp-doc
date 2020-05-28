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

当我们需要给前端返回一个数组形式的列表数据时，往往需要在响应信息的`data`键传入对应的二维数组，`jsonList`与`jsonData`的区别在于`jsonList`会在你传入的`data`数据外层套一层`list`的`key`。好处就是方便前端遍历处理，如果是直接传入`data`，我们将得到如下的`json`数据：

```php
$data = [
    [
        "id"       => 1,
        "username" => "zhangsan",
        "email"    => "abc@domain.com",
        "phone"    => "186****1234"
    ],
    [
        "id"       => 2,
        "username" => "lisi",
        "email"    => "123@domain.com",
        "phone"    => "186****5678"
    ],
];

return SendMsg::jsonData($data);
```

得到结果：

```json
{
    "code":10000,
    "message":"success",
    "data":[
        {
            "id":1,
            "username":"zhangsan",
            "email":"abc@domain.com",
            "phone":"186****1234"
        },
        {
            "id":2,
            "username":"lisi",
            "email":"123@domain.com",
            "phone":"186****5678"
        }
    ],
    "operate":"index/User/index"
}
```

我们可以发现，上面的返回数据并不是一个标准的`json`对象形式，前端获取的`data`实际是一个数组，这不利于前端获取数据遍历循环。而使用`jsonList`返回的就是如下效果：

```php
$data = [
    [
        "id"       => 1,
        "username" => "zhangsan",
        "email"    => "abc@domain.com",
        "phone"    => "186****1234"
    ],
    [
        "id"       => 2,
        "username" => "lisi",
        "email"    => "123@domain.com",
        "phone"    => "186****5678"
    ],
];

return SendMsg::jsonList($data);
```

得到结果：

```json
{
    "code":10000,
    "message":"success",
    "data":{
        "list":[
            {
                "id":1,
                "username":"zhangsan",
                "email":"abc@domain.com",
                "phone":"186****1234"
            },
            {
                "id":2,
                "username":"lisi",
                "email":"123@domain.com",
                "phone":"186****5678"
            }
        ]
    },
    "operate":"index/User/index"
}
```

这样前端就可以依据返回数据的`data.list`遍历循环数据，这样也同样避免了键名重复导致的前端代码可读性降低的问题。同样也方便前端封装请求与响应的公共方法。

如过你需求是在传入`list`的同时，还要增加其他额外的参数，那就需要服务端自行构建返回数据的结构了，例如使用ThinkPHP的分页功能返回数据，我们可以直接把查询出来的数据结果依旧传给`jsonData`方法进行处理得到：

```json
{
    "code":10000,
    "message":"success",
    "data":{
        "total":2,
        "per_page":15,
        "current_page":1,
        "last_page":1,
        "data":[
            {
                "id":1,
                "username":"zhangsan",
                "email":"abc@domain.com",
                "phone":"186****1234"
            },
            {
                "id":2,
                "username":"lisi",
                "email":"123@domain.com",
                "phone":"186****5678"
            }
        ]
    },
    "operate":"index/User/index"
}
```

同样如果你选择使用`jsonList`也可以。具体如何定义，还需要前后端配合协商处理。

#### 直接抛出信息--jsonThrow

> SendMsg::jsonThrow\("错误信息", "code码", "附加数据（支持数组）", "http状态码（默认200）", "header信息（数组）"\);

有些使用场景需要我们直接抛出异常，并直接响应给客户端，我们就可以使用`jsonThrow`的方式进行抛出。使用了`jsonThrow`后，代码执行将在此句终止，并直接响应给客户端或浏览器，例如：你在控制器中调用的任何其他类方法中直接使用`jsonThrow`返回的话，将会直接抛出你的异常信息：

```php
return SendMsg::jsonThrow("请先绑定用户");
```
```json
{
    "code":40000,
    "message":"请先绑定用户",
    "data":[

    ],
    "operate":"index/User/index"
}
```

`jsonThrow`方法其实就是常规的throw抛出，但是如果您的方法被try catch捕获，那么将不会直接抛出响应了。`jsonThrow`方法的参数使用规则与`jsonAlert`基本一致，区别就是一个是强制抛出，一个是正常响应。

我们不推荐全部以`jsonThrow`这种方式进行响应，好的编码习惯，业务逻辑就应该尽量不使用强制抛出，而是try catch逐层返回。所以`SendMsg`提供另一种响应使用方案`arrayData`或`arrayAlert`结合`jsonSend`进行响应。

#### 向客户端发送响应--jsonSend

> SendMsg::jsonSend\("`arrayData`或`arrayAlert`返回的数据数组", "是否`data`键数据返回`list`形式（bool值）", "http状态码（默认200）", "header信息（数组）"\);

通常情况下上面的`jsonData`、`jsonList`、`jsonAlert`、`jsonThrow`四个方法已经基本满足我们需求了。

如果你有类似调用第三方接口或者有需要在其他的扩展类方法或是模型方法中返回一个操作是否成功的逻辑判断的返回需求时，通常我们的做法是返回一个`array`数组，数组里会包含是否成功的`key`和成功的数据或者是失败的提示：

```php
public function edit()
{
    $update = (new User())->where("id",1)->update(["username"=>"lisi"]);
    if($update){
        return [
           "status" => 1,
           "msg" => "编辑成功" 
        ];
    }
    return [
        "status" => 0,
        "msg" => "编辑失败" 
    ];
}
```

上面仅是一个简单的示例，实际我们的业务可能要比它复杂很多，为了保持这样的返回数据全局统一规范，所以衍生出`arrayData`和`arrayAlert`两种仅返回数组的方法供开发者使用。

> SendMsg::arrayData\("数据内容（支持数组）","http状态码（默认200）"\);

> SendMsg::arrayAlert\("code码", "错误信息", "附加数据（支持数组）", "是否强制抛出（bool值）", "http状态码（默认200）"\);

* `arrayData`返回的`code`固定是`10000`，`arrayAlert`返回的`code`默认是`40000`（可自定义）。

* `是否强制抛出`表示是否以throw方式响应，效果类似于`jsonThrow`，我们更推荐这样的需求直接使用`jsonThrow`方法进行响应。

* `jsonSend`的第二个参数为`true`时返回值的效果与`jsonList`相同。

```php
$data = [
    "id"       => 1,
    "username" => "zhangsan",
    "email"    => "abc@domain.com",
    "phone"    => "186****1234"
];
// 注意：我们不能在控制器直接返回 arrayData 的数组
$res = SendMsg::arrayData($data);
// 所以才需要使用 jsonSend 来把数据传递给ThinkPHP的response对象进行客户端响应
return SendMsg::jsonSend($res);
```
上面仅是一个模拟示例，实际我们的`res`应该是调用的另一个类方法返回的信息，可能是成功，亦可能是失败：

```php
declare(strict_types=1);

namespace app\index\controller;

use app\index\service\UserService;
use lib\SendMsg;

class User extends Base
{
    public function index()
    {
        $res = (new UserService())->edit();
        return SendMsg::jsonSend($res);
    }
}

...

declare(strict_types=1);

namespace app\index\service;

use app\index\model\UserDao;

class UserService
{
    public function edit()
    {
        $detail = (new UserDao())->where("id",1)->find();
        if(empty($detail)){
            return SendMsg::arrayAlert(40001, "数据不存在");
        }
        $update = (new UserDao())->where("id",1)->update(["username"=>"lisi"]);
        if($update){
            return SendMsg::arrayData();
        }
        return SendMsg::arrayAlert(40002, "编辑失败");
    }
}
```

上面的示例将有可能返回三种情况：

1、操作成功

```json
{
    "code":10000,
    "message":"success",
    "data":[

    ],
    "operate":"index/User/index"
}
```

2、数据不存在

```json
{
    "code":40001,
    "message":"数据不存在",
    "data":[

    ],
    "operate":"index/User/index"
}
```

3、编辑失败

```json
{
    "code":40002,
    "message":"编辑失败",
    "data":[

    ],
    "operate":"index/User/index"
}
```

> 以上示例完全可以体现出`code`码在日常接口开发中的作用，开发者可以根据业务自定义`code`码的规则，前端根据不同的`code`码进行不同的操作处理。

事实上`jsonData`、`jsonList`、`jsonAlert`、`jsonThrow`四个方法也都是基于`arrayData`和`arrayAlert`实现的。