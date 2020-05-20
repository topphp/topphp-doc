## 空控制器

空控制器的概念是指当系统找不到指定的控制器名称的时候，系统会尝试定位当前应用下的空控制器\(`Error`\)类，利用这个机制我们可以用来定制错误页面和进行URL的优化。

例如，下面是单应用模式下，我们可以给项目定义一个`Error`控制器类。

```php
<?php
namespace app\controller;

class Error 
{
    public function __call($method, $args)
    {
        return 'error request!';
    }
}
```



