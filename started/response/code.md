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

> SendMsg::jsonData\("数据内容（支持数组）","http状态码（默认200）"\);

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




