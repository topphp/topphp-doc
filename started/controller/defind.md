## 控制器定义

控制器文件通常放在`controller`下面，类名和文件名保持大小写一致，并采用驼峰命名（首字母大写）。

如果要改变`controller`目录名，需要在`route.php`配置文件中设置：

```php
'controller_layer'    =>    'controllers',
```

如果使用的是单应用模式，那么控制器的类的定义如下：

```php
<?php
namespace app\controller;

class User 
{
    public function login()
    {
        return 'login';
    }
}
```

控制器类文件的实际位置则变成

```
app\controller\User.php
```

访问URL地址是（假设没有定义路由的情况下）

```
http://localhost/user/login
```

如果你的控制器是`HelloWorld`，并且定义如下：

```php
<?php
namespace app\controller;

class HelloWorld 
{
    public function hello()
    {
        return 'hello，world！';
    }
}
```

控制器类文件的实际位置是

```
app\controller\HelloWorld.php
```

访问URL地址是（假设没有定义路由的情况下）

```
http://localhost/index.php/HelloWorld/hello
```

并且也可以支持下面的访问URL

```
http://localhost/hello_world/hello
```

## 多应用模式

多应用模式下，控制器类定义仅仅是命名空间有所区别，例如：

```php
<?php
namespace app\shop\controller;

class User
{
    public function login()
    {
        return 'login';
    }
}
```

控制器类文件的实际位置是

```php
app\shop\controller\User.php
```

访问URL地址是（假设没有定义路由的情况下）

```
http://localhost/index.php/shop/user/login
```

## 控制器后缀

如果你希望避免引入同名模型类的时候冲突，可以在`route.php`配置文件中设置

```php
// 使用控制器后缀
'controller_suffix'     => true,
```

这样，上面的控制器类就需要改成

```php
<?php
namespace app\controller;

class UserController
{
    public function login()
    {
        return 'login';
    }
}
```

相应的控制器类文件也要改为

```
app\controller\UserController.php
```

## 渲染输出

默认情况下，控制器的输出全部采用`return`的方式，无需进行任何的手动输出，系统会自动完成渲染内容的输出。

下面都是有效的输出方式：

```php
<?php
namespace app\index\controller;

class Index 
{
    public function hello()
    {
    	// 输出hello,world!
        return 'hello,world!';
    }
    
    public function json()
    {
    	// 输出JSON
        return json($data);
    }
    
    public function read()
    {
    	// 渲染默认模板输出
        return view();
    }

}
```

> 控制器一般不需要任何输出，直接`return`即可。并且控制器在`json`请求会自动转换为`json`格式输出。
>
> 不要在控制器中使用包括`die`、`exit`在内的中断代码。如果你需要调试并中止执行，可以使用系统提供的`halt`助手函数。

```php
halt('输出测试');
```

## 多级控制器

支持任意层次级别的控制器，并且支持路由，例如：

```php
<?php
namespace app\index\controller\user;

class  Blog 
{
    public function index()
    {
        return 'index';
    }
    
}
```

该控制器类的文件位置为：

```
app/index/controller/user/Blog.php
```

访问地址可以使用

```
http://serverName/index.php/user.blog/index
```

由于URL访问不能访问默认的多级控制器（可能会把多级控制器名误识别为URL后缀），因此建议所有的多级控制器都通过路由定义后访问，如果要在路由定义中使用多级控制器，可以使用：

```php
Route::get('user/blog','user.blog/index');
```



