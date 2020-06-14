## 模板渲染

默认情况下，框架会自动定位你的模板文件路径，优先定位应用目录下的`view`目录，这种方式的视图目录下就是应用的控制器目录。

### 单应用模式

```php
├─app
│   └─view（视图目录）
│     ├─index            index控制器目录
│     │  └─index.html    index模板文件
│     └─ ...             更多控制器目录

```

### 多应用模式

```php
├─app
│  ├─app1 （应用1）
│  │   └─view（应用视图目录）
│  │   	 ├─index            index控制器目录
│  │     │  └─index.html    index模板文件
│  │ 	 └─ ...             更多控制器目录
│  │ 
│  └─ app2... （更多应用）

```

第二种方式是视图文件和应用类库文件完全分离，统一放置在根目录下的`view`目录。

### 单应用模式

```php
├─view                 视图文件目录
│   ├─index            index控制器目录
│   │  └─index.html    index模板文件
│   └─ ...             更多控制器目录

```

### 多应用模式

如果是多应用模式的话，这种方式下`view`目录下面首先是应用子目录。

```php
├─view                 视图文件目录
│  ├─index（应用视图目录）
│  │   ├─index            index控制器目录
│  │   │  └─index.html    index模板文件
│  │   └─ ...             更多控制器目录

```

如果你需要自定义`view`目录名称，可以通过设置`view_dir_name`配置参数。

```php
'view_dir_name'    =>    'template',
```

## 模板渲染

模板渲染的最典型用法是直接使用`fetch`方法，不带任何参数：

```php
<?php
namespace app\index\controller;

use think\facade\View;

class Index
{
    public function index()
    {
        // 不带任何参数 自动定位当前操作的模板文件
        return View::fetch();
    }
}
```

表示系统会按照默认规则自动定位视图目录下的模板文件，其规则是：

```php
控制器名（小写+下划线）/操作名.html

```

默认的模板文件名规则改为实际操作方法名的小写+下划线写法。但可以配置`auto_rule`参数的值来改变当前操作的自动渲染规则。

| auto\_rule配置 | 自动定位规则 |
| :--- | :--- |
| 1 | 操作方法的小写+下划线 |
| 2 | 操作方法全部转换小写 |
| 3 | 保持和操作方法一致 |

如果有更改模板引擎的`view_depr`设置（假设`'view_depr'=>'_'`）的话，则上面的自动定位规则变成：

```php
控制器（小写+下划线）_操作.html

```

如果没有按照模板定义规则来定义模板文件（或者需要调用其他控制器下面的某个模板），可以使用：

```php
// 指定模板输出
return View::fetch('edit'); 
```

表示调用当前控制器下面的edit模板

```php
return View::fetch('member/read');
```

表示调用Member控制器下面的read模板。

跨应用渲染模板

```php
return View::fetch('admin@member/edit');
```

渲染输出不需要写模板文件的路径和后缀。这里面的控制器和操作并不一定需要有实际对应的控制器和操作，只是一个目录名称和文件名称而已，例如，你的项目里面可能根本没有Public控制器，更没有Public控制器的menu操作，但是一样可以使用

```php
return View::fetch('public/menu');
```

输出这个模板文件。理解了这个，模板输出就清晰了。

支持从视图根目录开始读取模板，例如：

```php
return View::fetch('/menu');
```

表示读取的模板是

```php
menu.html

```

如果你的模板文件位置比较特殊或者需要自定义模板文件的位置，可以采用下面的方式处理。

```php
return View::fetch('../template/public/menu.html');
```

这种方式需要带模板路径和后缀指定一个完整的模板文件位置，这里的`../template/public`目录是相对于当前项目入口文件位置。如果是其他的后缀文件，也支持直接输出，例如：

```php
return View::fetch('../template/public/menu.tpl');
```

只要`../template/public/menu.tpl`是一个实际存在的模板文件。

> 要注意模板文件位置是相对于应用的入口文件，而不是模板目录。

## 助手函数

可以使用系统提供的助手函数`view`，可以完成相同的功能：

```php
namespace app\index\controller;

class Index 
{
    public function index()
    {
        // 渲染模板输出
        return view('hello', ['name' => 'thinkphp']);
    }
}
```

## 渲染内容

如果希望直接解析内容而不通过模板文件的话，可以使用`display`方法：

```php
namespace app\index\controller;

use think\facade\View;

class Index 
{
    public function index()
    {
        // 直接渲染内容
        $content = '{$name}-{$email}';
        return View::display($content, ['name' => 'thinkphp', 'email' => 'thinkphp@qq.com']);
    }
}
```

渲染的内容中一样可以使用模板引擎的相关标签。

