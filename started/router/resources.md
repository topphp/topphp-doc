## 资源路由

支持设置`RESTFul`请求的资源路由，方式如下：

```php
Route::resource('blog', 'Blog');
```

表示注册了一个名称为`blog`的资源路由到`Blog`控制器，系统会自动注册7个路由规则，如下：

| 标识 | 请求类型 | 生成路由规则 | 对应操作方法（默认） |
| :--- | :--- | :--- | :--- |
| index | GET | `blog` | index |
| create | GET | `blog/create` | create |
| save | POST | `blog` | save |
| read | GET | `blog/:id` | read |
| edit | GET | `blog/:id/edit` | edit |
| update | PUT | `blog/:id` | update |
| delete | DELETE | `blog/:id` | delete |

具体指向的控制器由路由地址决定，你只需要为`Blog`控制器创建以上对应的操作方法就可以支持下面的URL访问：

```php
http://serverName/blog/
http://serverName/blog/128
http://serverName/blog/28/edit
```

Blog控制器中的对应方法如下：

```php
<?php
namespace app\controller;

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

可以通过命令行快速创建一个资源控制器类（参考后面的控制器章节的资源控制器一节）。

可以改变默认的id参数名，例如：

```php
Route::resource('blog', 'Blog')
    ->vars(['blog' => 'blog_id']);
```

控制器的方法定义需要调整如下：

```php
<?php
namespace app\controller;

class Blog
{
    public function index()
    {
    }

    public function read($blog_id)
    {
    }

    public function edit($blog_id)
    {
    }
}
```

也可以在定义资源路由的时候限定执行的方法（标识），例如：

```php
// 只允许index read edit update 四个操作
Route::resource('blog', 'Blog')
    ->only(['index', 'read', 'edit', 'update']);

// 排除index和delete操作
Route::resource('blog', 'Blog')
    ->except(['index', 'delete']);
```

资源路由的标识不可更改，但生成的路由规则和对应操作方法可以修改。

如果需要更改某个资源路由标识的对应操作，可以使用下面方法：

```php
Route::rest('create',['GET', '/add','add']);
```

设置之后，URL访问变为：

```
http://serverName/blog/create
变成
http://serverName/blog/add
```

创建blog页面的对应的操作方法也变成了add。

支持批量更改，如下：

```php
Route::rest([
    'save'   => ['POST', '', 'store'],
    'update' => ['PUT', '/:id', 'save'],
    'delete' => ['DELETE', '/:id', 'destory'],
]);
```

## 资源嵌套

支持资源路由的嵌套，例如：

```php
Route::resource('blog', 'Blog');
Route::resource('blog.comment','Comment');
```

就可以访问如下地址：

```
http://serverName/blog/128/comment/32
http://serverName/blog/128/comment/32/edit

```

生成的路由规则分别是：

```
blog/:blog_id/comment/:id
blog/:blog_id/comment/:id/edit

```

Comment控制器对应的操作方法如下：

```php
<?php

namespace app\controller;

class Comment
{
    public function edit($id, $blog_id)
    {
    }
}
```

edit方法中的参数顺序可以随意，但参数名称必须满足定义要求。

如果需要改变其中的变量名，可以使用：

```php
// 更改嵌套资源路由的blog资源的资源变量名为blogId
Route::resource('blog.comment', 'index/comment')
    ->vars(['blog' => 'blogId']);
```

Comment控制器对应的操作方法改变为：

```php
<?php
namespace app\controller;

class Comment
{
    public function edit($id, $blogId)
    {
    }
}
```



