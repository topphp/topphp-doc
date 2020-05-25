## Code 响应

通常情况下，我们开发一个前后端分离的项目，都需要自己定义一套响应数据的结构，而最常见的跨语言数据结构就是json了。

`TopPHP`考虑到这种需求的广泛度，骨架提供了`SendMsg`类来规范`Code`响应规则，通常也是最常见的规则。

```js
// 操作成功的响应示例
{"code":10000,"message":"success","data":{"id":"1"}}

// 操作失败的响应示例
{"code":40000,"message":"系统异常","data":[]}
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

访问`index/User/index`我们将得到如下结果:

```js
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

我们还可以



