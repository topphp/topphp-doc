## 模板变量

## 模板赋值

模板中的变量（除了一些系统变量外）必须先进行模板赋值后才能使用，可以使用`assign`方法进行全局模板变量赋值。

```php
namespace app\controller;

use think\facade\View;

class Index
{
    public function index()
    {
        // 模板变量赋值
        View::assign('name','ThinkPHP');
        View::assign('email','thinkphp@qq.com');
        // 或者批量赋值
        View::assign([
            'name'  => 'ThinkPHP',
            'email' => 'thinkphp@qq.com'
        ]);
        // 模板输出
        return View::fetch('index');
    }
}
```

`assign`方法赋值属于全局变量赋值，如果你需要单次赋值的话，可以直接在`fetch`方法中传入。

```php
namespace app\controller;

use think\facade\View;

class Index
{
    public function index()
    {
        // 模板输出并变量赋值
        return View::fetch('index', [
            'name'  => 'ThinkPHP',
            'email' => 'thinkphp@qq.com'
        ]);
    }
}
```

### 助手函数

如果使用`view`助手函数渲染输出的话，可以使用下面的方法进行模板变量赋值：

```php
return view('index', [
    'name'  => 'ThinkPHP',
    'email' => 'thinkphp@qq.com'
]);
```

助手函数的变量赋值也是当次模板渲染有效。

