## 参数绑定

参数绑定是把当前请求的变量作为操作方法（也包括架构方法）的参数直接传入，参数绑定并不区分请求类型。

> 参数绑定传入的值会经过全局过滤，如果你有额外的过滤需求可以在操作方法中单独处理。

参数绑定方式默认是按照变量名进行绑定，例如，我们给`Blog`控制器定义了两个操作方法`read`和`archive`方法，由于`read`操作需要指定一个`id`参数，`archive`方法需要指定年份（`year`）和月份（`month`）两个参数，那么我们可以如下定义：

```php
<?php
namespace app\controller;

class Blog 
{
    public function read($id)
    {
        return 'id=' . $id;
    }

    public function archive($year, $month='01')
    {
        return 'year=' . $year . '&month=' . $month;
    }
}
```

> 注意这里的操作方法并没有具体的业务逻辑，只是简单的示范。

URL的访问地址分别是：

```
http://serverName/index.php/blog/read/id/5
http://serverName/index.php/blog/archive/year/2016/month/06
```

两个URL地址中的`id`参数和`year`和`month`参数会自动和`read`操作方法以及`archive`操作方法的`同名参数`绑定。

> 变量名绑定不一定由访问URL决定，路由地址也能起到相同的作用

输出的结果依次是：

```php
id=5
year=2016&month=06
```

按照变量名进行参数绑定的参数必须和URL中传入的变量名称一致，但是参数顺序不需要一致。也就是说

```
http://serverName/index.php/blog/archive/month/06/year/2016
```

和上面的访问结果是一致的，URL中的参数顺序和操作方法中的参数顺序都可以随意调整，关键是确保参数名称一致即可。

如果用户访问的URL地址是：

```
http://serverName/index.php/blog/read

```

那么会抛出下面的异常提示：`参数错误:id`

报错的原因很简单，因为在执行read操作方法的时候，id参数是必须传入参数的，但是方法无法从URL地址中获取正确的id参数信息。由于我们不能相信用户的任何输入，因此建议你给read方法的id参数添加默认值，例如：

```php
public function read($id = 0)
{
    return 'id=' . $id;
}
```

这样，当我们访问`http://serverName/index.php/blog/read/`的时候 就会输出

```php
id=0
```

> 始终给操作方法的参数定义默认值是一个避免报错的好办法（依赖注入参数除外）

为了更好的配合前端规范，支持自动识别小写+下划线的请求变量使用驼峰注入，例如：

```
http://serverName/index.php/blog/read/blog_id/5
```

可以使用下面的方式接收`blog_id`变量，所以请确保在方法的参数使用驼峰（首字母小写）规范。

```php
public function read($blogId = 0)
{
    return 'id=' . $blogId;
}
```



