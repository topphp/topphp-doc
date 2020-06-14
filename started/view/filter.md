## 视图过滤

可以对视图的渲染输出进行过滤

```php
<?php
namespace app\index\controller;

use think\facade\View;

class Index 
{
    public function index()
    {
        // 使用视图输出过滤
        return View::filter(function($content){
        	return str_replace("\r\n",'<br/>',$content);
        })->fetch();
    }
}
```

如果使用`view`助手函数进行模板渲染输出的话，可以使用下面的方式

```php
<?php
namespace app\index\controller;

class Index 
{
    public function index()
    {
        // 使用视图输出过滤
        return view()->filter(function($content){
        	return str_replace("\r\n",'<br/>',$content);
        });
    }
}
```



