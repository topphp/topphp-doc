## 模板引擎

## 使用`thinkTemplate`模板引擎

新版框架默认只能支持PHP原生模板，如果需要使用`thinkTemplate`模板引擎，需要安装`think-view`扩展（该扩展会自动安装`think-template`依赖库）。

```php
composer require topthink/think-view

```

### 配置文件

安装完成后，在配置目录的`view.php`文件中进行模板引擎相关参数的配置，例如：

```php
return [
    // 模板引擎类型
    'type'         => 'Think',
    // 模板路径
    'view_path'    => './template/',
    // 模板后缀
    'view_suffix'  => 'html',
    // 模板文件名分隔符
    'view_depr'    => '/',
    // 模板引擎普通标签开始标记
    'tpl_begin'    => '{',
    // 模板引擎普通标签结束标记
    'tpl_end'      => '}',
    // 标签库标签开始标记
    'taglib_begin' => '{',
    // 标签库标签结束标记
    'taglib_end'   => '}',
];
```

### 调用`engine`方法初始化

视图类也提供了`engine`方法对模板解析引擎进行初始化或者切换不同的模板引擎，例如：

```php
<?php
namespace app\index\controller;

use think\facade\View;

class Index
{
    public function index()
    {
        // 使用内置PHP模板引擎渲染模板输出
        return View::engine('php')->fetch();
    }
}
```

表示当前视图的模板文件使用原生php进行解析。

> 如果你需要动态改变模板引擎的参数，请使用视图类提供的`config`方法进行动态设置，而不要使用改变配置类参数的方式。

```php
<?php
namespace app\index\controller;

use think\facade\View;

class Index 
{
    public function index()
    {
        // 改变当前操作的模板路径
        View::config(['view_path' => 'mypath']);
        return View::fetch();
    }
}
```

关于模板引擎的标签用法，可以[参考这里](https://www.kancloud.cn/manual/think-template/content)。



