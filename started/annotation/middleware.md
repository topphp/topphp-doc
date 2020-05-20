## 注解中间件

定义好中间件后，在控制器中我们可以通过如下注解的方式精确到操作来调用中间件：
```php
<?php
namespace app\index\controller;

use app\middleware\User;
use think\annotation\route\Middleware;

class Index
{
    /**
     * index
     * @return mixed
     * @Route("index")
     * @Middleware({User::class})
     */
    public function index()
    {
        return "Hello TopPHP!";
    }
}
```



