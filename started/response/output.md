## 响应输出

大多数情况，我们不需要关注`Response`对象本身，只需要在控制器的操作方法中返回数据即可。

最简单的响应输出是直接在路由闭包或者控制器操作方法中返回一个字符串，例如：

```php
Route::get('hello/:name', function ($name) {
    return 'Hello,' . $name . '!';
});
```

```php
<?php
namespace app\controller;

class Index
{
    public function hello($name='thinkphp')
    {
        return 'Hello,' . $name . '!';
    }
}
```

由于默认是输出`Html`输出，所以直接以html页面方式输出响应内容。如果你发起一个JSON请求的话，输出就会自动使用JSON格式响应输出。

为了规范和清晰起见，最佳的方式是在控制器最后明确输出类型（毕竟一个确定的请求是有明确的响应输出类型），默认支持的输出类型包括：

| 输出类型 | 快捷方法 | 对应Response类 |
| :--- | :--- | :--- |
| HTML输出 | response | \think\Response |
| 渲染模板输出 | view | \think\response\View |
| JSON输出 | json | \think\response\Json |
| JSONP输出 | jsonp | \think\response\Jsonp |
| XML输出 | xml | \think\response\Xml |
| 页面重定向 | redirect | \think\response\Redirect |
| 附件下载 | download | \think\response\Download |

每一种输出类型其实对应了一个不同的`Response`子类（`response()`函数对应的是`Response`基类），也可以在应用中自定义`Response`子类满足特殊需求的输出。

例如我们需要输出一个JSON数据给客户端（或者AJAX请求），可以使用：

```php
<?php
namespace app\controller;

class Index
{
    public function hello()
    {
        $data = ['name' => 'thinkphp', 'status' => '1'];
        return json($data);
    }
}
```

> 这些助手函数的返回值都是`Response`类或者子类的对象实例，所以后续可以调用`Response`基类或者当前子类的相关方法，后面我们会讲解相关方法。

如果你只需要输出一个html格式的内容，可以直接使用

```php
<?php
namespace app\controller;

class Index
{
    public function hello()
    {
        $data = 'Hello,ThinkPHP!';
        return response($data);
    }
}
```

或者使用`return`直接返回输出的字符串。

```php
<?php
namespace app\controller;

class Index
{
    public function hello()
    {
        return 'Hello,ThinkPHP!';
    }
}
```

  


