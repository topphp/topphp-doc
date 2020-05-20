## 路由绑定

可以使用路由绑定简化URL或者路由规则的定义，绑定支持如下方式：

## 绑定到控制器/操作

把当前的URL绑定到控制器/操作，最多支持绑定到操作级别，例如在路由定义文件中添加：

```php
// 绑定当前的URL到 Blog控制器
Route::bind('blog');
// 绑定当前的URL到 Blog控制器的read操作
Route::bind('blog/read');
```

该方式针对路由到控制器/操作有效，假如我们绑定到了blog控制器，那么原来的访问URL从

```
http://serverName/blog/read/id/5
```

可以简化成

```
http://serverName/read/id/5
```

如果定义了路由

```
Route::get('blog/:id','blog/read');
```

那么访问URL就变成了

```
http://serverName/5
```

## 绑定到命名空间

把当前的URL绑定到某个指定的命名空间，例如：

```php
// 绑定命名空间
Route::bind(':\app\index\controller');
```

那么，我们接下来只需要通过

```
http://serverName/blog/read/id/5
```

就可以直接访问`\app\index\controller\Blog`类的read方法。

## 绑定到类

把当前的URL直接绑定到某个指定的类，例如：

```php
// 绑定到类
Route::bind('\app\index\controller\Blog');
```

那么，我们接下来只需要通过

```
http://serverName/read/id/5
```

就可以直接访问`\app\index\controller\Blog`类的read方法。

