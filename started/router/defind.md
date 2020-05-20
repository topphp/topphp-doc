## 路由定义

要使用`Route`类注册路由必须首先在路由定义文件开头添加引用（后面不再重复说明）

```php
use think\facade\Route;
```

### 注册路由

最基础的路由定义方法是：

> Route::rule\('路由表达式', '路由地址', '请求类型'\);

例如注册如下路由规则（假设为单应用模式）：

```php
// 注册路由到News控制器的read操作
Route::rule('new/:id','News/read');
```

我们访问：

```php
http://serverName/new/5
```

会自动路由到：

```
http://serverName/news/read/id/5
```

并且原来的访问地址会自动失效。

可以在`rule`方法中指定请求类型（不指定的话默认为任何请求类型有效），例如：

```php
Route::rule('new/:id', 'News/update', 'POST');
```

> 请求类型参数不区分大小写。

表示定义的路由规则在`POST`请求下才有效。如果要定义`GET`和`POST`请求支持的路由规则，可以用：

```php
Route::rule('new/:id','News/read','GET|POST');
```

不过通常我们更推荐使用对应请求类型的快捷方法，包括：

| 类型 | 描述 | 快捷方法 |
| :--- | :--- | :--- |
| GET | GET请求 | get |
| POST | POST请求 | post |
| PUT | PUT请求 | put |
| DELETE | DELETE请求 | delete |
| PATCH | PATCH请求 | patch |
| \* | 任何请求类型 | any |

快捷注册方法的用法为：

> Route::快捷方法名\('路由表达式', '路由地址'\);

使用示例如下：

```php
Route::get('new/<id>','News/read'); // 定义GET请求路由规则
Route::post('new/<id>','News/update'); // 定义POST请求路由规则
Route::put('new/:id','News/update'); // 定义PUT请求路由规则
Route::delete('new/:id','News/delete'); // 定义DELETE请求路由规则
Route::any('new/:id','News/read'); // 所有请求都支持的路由规则
```

注册多个路由规则后，系统会依次遍历注册过的满足请求类型的路由规则，一旦匹配到正确的路由规则后则开始执行最终的调度方法，后续规则就不再检测。

### 规则表达式

规则表达式通常包含静态规则和动态规则，以及两种规则的结合，例如下面都属于有效的规则表达式：

```php
Route::rule('/', 'index'); // 首页访问路由
Route::rule('my', 'Member/myinfo'); // 静态地址路由
Route::rule('blog/:id', 'Blog/read'); // 静态地址和动态地址结合
Route::rule('new/:year/:month/:day', 'News/read'); // 静态地址和动态地址结合
Route::rule(':user/:blog_id', 'Blog/read'); // 全动态地址
```

> 规则表达式的定义以`/`为参数分割符（无论你的`PATH_INFO`分隔符设置是什么，请确保在定义路由规则表达式的时候统一使用`/`进行URL参数分割，除非是使用组合变量的情况）。

每个参数中可以包括动态变量，例如`:变量`或者`<变量>`都表示动态变量（新版推荐使用第二种方式，更利于混合变量定义），并且会自动绑定到操作方法的对应参数。

> 你的URL访问`PATH_INFO`分隔符使用`pathinfo_depr`配置，但无论如何配置，都不影响路由的规则表达式的路由分隔符定义。

### 可选变量

支持对路由参数的可选定义，例如：

```php
Route::get('blog/:year/[:month]','Blog/archive');
// 或者
Route::get('blog/<year>/<month?>','Blog/archive');
```

变量用`[ ]`包含起来后就表示该变量是路由匹配的可选变量。

以上定义路由规则后，下面的URL访问地址都可以被正确的路由匹配：

```
http://serverName/index.php/blog/2015
http://serverName/index.php/blog/2015/12
```

采用可选变量定义后，之前需要定义两个或者多个路由规则才能处理的情况可以合并为一个路由规则。

> 可选参数只能放到路由规则的最后，如果在中间使用了可选参数的话，后面的变量都会变成可选参数。

### 完全匹配

规则匹配检测的时候默认只是对URL从头开始匹配，只要URL地址开头包含了定义的路由规则就会匹配成功，如果希望URL进行完全匹配，可以在路由表达式最后使用`$`符号，例如：

```php
Route::get('new/:cate$', 'News/category');
```

这样定义后

```
http://serverName/index.php/new/info
```

会匹配成功,而

```
http://serverName/index.php/new/info/2
```

则不会匹配成功。

如果是采用

```php
Route::get('new/:cate', 'News/category');
```

方式定义的话，则两种方式的URL访问都可以匹配成功。

如果需要全局进行URL完全匹配，可以在路由配置文件中设置

```php
// 开启路由完全匹配
'route_complete_match'   => true,
```

开启全局完全匹配后，如果需要对某个路由关闭完全匹配，可以使用

```
Route
:
:
get
(
'new/:cate'
,
'News/category'
)
-
>
completeMatch
(
false
)
;
```

### 额外参数

在路由跳转的时候支持额外传入参数对（额外参数指的是不在URL里面的参数，隐式传入需要的操作中，有时候能够起到一定的安全防护作用，后面我们会提到）。例如：

```php
Route::get('blog/:id','blog/read')
    ->append(['status' => 1, 'app_id' =>5]);
```

上面的路由规则定义中`status`和`app_id`参数都是URL里面不存在的，属于隐式传值。可以针对不同的路由设置不同的额外参数。

> 如果`append`方法中的变量和路由规则存在冲突的话，append方法传入的优先。

### 路由标识

如果你需要快速的根据路由生成URL地址，可以在定义路由的时候指定生成标识（但要确保唯一）。

例如

```php
// 注册路由到News控制器的read操作
Route::rule('new/:id','News/read')
    ->name('new_read');
```

生成路由地址的时候就可以使用

```php
url('new_read', ['id' => 10]);
```

如果不定义路由标识的话，系统会默认使用路由地址作为路由标识，例如可以使用下面的方式生成

```php
url('News/read', ['id' => 10]);
```

### 强制路由

在路由配置文件中设置

```php
'url_route_must'        =>  true,
```

将开启强制使用路由，这种方式下面必须严格给每一个访问地址定义路由规则（**包括首页**），否则将抛出异常。

首页的路由规则采用`/`定义即可，例如下面把网站首页路由输出`Hello,world!`

```php
Route::get('/', function () {
    return 'Hello,world!';
});
```



