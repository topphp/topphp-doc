当前的请求对象由`think\Request`类负责，该类不需要单独实例化调用，通常使用依赖注入即可。在其它场合则可以使用`think\facade\Request`静态类操作。

> 项目里面应该使用`app\Request`对象，该对象继承了系统的`think\Request`对象，但可以增加自定义方法或者覆盖已有方法。项目里面已经在`provider.php`中进行了定义，所以你仍然可以和之前一样直接使用容器和静态代理操作请求对象。

## 构造方法注入

一般适用于没有继承系统的控制器类的情况。

```php
<?php

namespace app\index\controller;

use think\Request;

class Index 
{
    /**
     * @var \think\Request Request实例
     */
    protected $request;
    
    /**
     * 构造方法
     * @param Request $request Request对象
     * @access public
     */
    public function __construct(Request $request)
    {
		$this->request = $request;
    }
    
    public function index()
    {
		return $this->request->param('name');
    }    
}
```

## 操作方法注入

另外一种选择是在每个方法中使用依赖注入。

```php
<?php

namespace app\index\controller;

use think\Request;

class Index
{
    
    public function index(Request $request)
    {
		return $request->param('name');
    }    
}
```

无论是否继承系统的控制器基类，都可以使用操作方法注入。

> 更多关于依赖注入的内容，请参考依赖注入章节。

## 静态调用

在没有使用依赖注入的场合，可以通过`Facade`机制来静态调用请求对象的方法（注意`use`引入的类库区别）。

```php
<?php

namespace app\index\controller;

use think\facade\Request;

class Index
{
    
    public function index()
    {
		return Request::param('name');
    }    
}
```

该方法也同样适用于依赖注入无法使用的场合。

## 助手函数

为了简化调用，系统还提供了`request`助手函数，可以在任何需要的时候直接调用当前请求对象。

```php
<?php

namespace app\index\controller;


class Index
{

    public function index()
    {
        return request()->param('name');
    }
}
```

## 自定义请求对象

你可以在项目里面自定义Request对象，修改已有的方法或者增加新的方法，默认已经在项目里面为你准备了`app\Request`类，你只需要直接修改该类就可以为你的项目单独自定义请求对象。

自定义请求对象不支持为多应用的某个应用自定义，只能是全局自定义，如果你需要为某个应用定义不同的请求对象，可以在入口文件里面修改。例如：

```php
// 执行HTTP应用并响应
$request = new app\common\Request();
$http = (new App())->http;
$response = $http->run($request);
$response->send();
$http->end($response);
```

  


