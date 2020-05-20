## 注解路由

ThinkPHP支持使用注解方式定义路由（也称为注解路由），如果需要使用注解路由需要安装额外的扩展：

```
composer require topthink/think-annotation

```

>TopPHP骨架默认已安装该组件，开发者可以直接使用

然后只需要直接在控制器类的方法注释中定义，例如：

```php
<?php
namespace app\controller;

use think\annotation\Route;

class Index
{
    /**
     * @param  string $name 数据名称
     * @return mixed
     * @Route("hello/:name")
     */
	public function hello($name)
    {
    	return 'hello,'.$name;
    }
}
```

`@Route("hello/:name")`就是注解路由的内容，请务必注意注释的规范，不能在注解路由里面使用单引号，否则可能导致注解路由解析失败，可以利用IDE生成规范的注释。如果你使用`PHPStorm`的话，建议安装`PHP Annotations`插件：[https://plugins.jetbrains.com/plugin/7320-php-annotations](https://plugins.jetbrains.com/plugin/7320-php-annotations)，可以支持注解的自动完成。

> 该方式定义的路由在调试模式下面实时生效，部署模式则在第一次访问的时候生成注解缓存。

然后就使用下面的URL地址访问：

```
http://tp5.com/hello/thinkphp

```

页面输出

```
hello,thinkphp

```

默认注册的路由规则是支持所有的请求，如果需要指定请求类型，可以在第二个参数中指定请求类型：

```php
<?php
namespace app\controller;

use think\annotation\Route;

class Index
{
    /**
     * @param  string $name 数据名称
     * @return mixed
     * @Route("hello/:name", method="GET")
     */
	public function hello($name)
    {
    	return 'hello,'.$name;
    }
}
```

如果有路由参数需要定义，可以直接在后面添加方法，例如：

```php
<?php
namespace app\controller;

use think\annotation\Route;

class Index
{
    /**
     * @param string $name 数据名称
     * @Route('hello/:name', method="GET", https=1, ext="html")
     * @return mixed
     */
	public function hello($name)
    {
    	return 'hello,'.$name;
    }
}
```

支持在类的注释里面定义资源路由，例如：

```php
<?php
namespace app\controller;

use think\annotation\route\Resource;

/**
 * @Resource("blog")
 */
class Blog
{
    public function index()
    {
    }

    public function read($id)
    {
    }

    public function edit($id)
    {
    }
}
```

如果需要定义路由分组，可以使用

```php
<?php
namespace app\controller;

use think\annotation\route\Group;
use think\annotation\route\Route;

/**
 * @Group("blog")
 */
class Blog
{
    /**
     * @param  string $name 数据名称
     * @return mixed
     * @Route("hello/:name", method="GET")
     */
	public function hello($name)
    {
    	return 'hello,'.$name;
    }
}
```

当前控制器中的注解路由会自动加入`blog`分组下面，最终，会注册一个`blog/hello/:name`的路由规则。你一样可以对该路由分组设置公共的参数，例如：

```php
<?php
namespace app\controller;

use think\annotation\route\Middleware;
use think\annotation\route\Group;
use think\annotation\route\Route;
use think\middleware\SessionInit;

/**
 * @Group("blog",ext="html")
 * @Middleware({SessionInit::class})
 */
class Blog
{
    /**
     * @param  string $name 数据名称
     * @return mixed
     * @Route("hello/:name",method="GET")
     */
	public function hello($name)
    {
    	return 'hello,'.$name;
    }
}
```



