## 视图

视图功能由`\think\View`类配合视图驱动（也即模板引擎驱动）类一起完成，新版仅内置了PHP原生模板引擎（主要用于内置的异常页面输出），如果需要使用其它的模板引擎需要单独安装相应的模板引擎扩展。

如果你需要使用`think-template`模板引擎，只需要安装`think-view`模板引擎驱动。

```bash
composer require topthink/think-view

```

> 视图相关的配置在配置目录的`view.php`配置文件中进行定义。

通常可以直接使用`think\facade\View`来操作视图。



