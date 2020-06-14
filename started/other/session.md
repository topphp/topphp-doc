## Session

## 概述

可以直接使用`think\facade\Session`类操作`Session`。

> 新版本不支持操作原生`$_SESSION`数组和所有`session_`开头的函数，只能通过`Session`类（或者助手函数）来操作。会话数据统一在当前请求结束的时候统一写入 所以不要在`session`写入操作之后执行`exit`等中断操作,否则会导致`Session`数据写入失败。

`6.0`的`Session`类可以很好的支持诸如`Swoole`/`Workerman`等环境。

## 开启Session

`Session`功能默认是没有开启的（API应用通常不需要使用`Session`），如果你需要使用`Seesion`，需要在全局的中间件定义文件中加上下面的中间件定义：

```
'think\middleware\SessionInit'
```

> 如果是多应用模式，并且你只是用于部分应用，那么也可以在应用中间件定义文件中单独开启。

## Session初始化

系统会自动按照`session.php`配置的参数自动初始化`Session`。

默认支持的`session`设置参数包括：

| 参数 | 描述 |
| :--- | :--- |
| type | session类型（`File`或者`Cache`） |
| store | 当type设置为cache类型的时候指定存储标识 |
| expire | session过期时间（秒）必须大于0 |
| var\_session\_id | 请求session\_id变量名 |
| name | session\_name |
| prefix | session前缀 |
| serialize | 序列化方法 |

无需任何操作就可以直接调用`Session`类的相关方法，例如：

```php
Session::set('name', 'thinkphp');
Session::get('name');
```

会话数据保存（请求结束）的时候会自动序列化，并在读取的时候自动反序列化，默认使用`serialize`/`unserialize`进行序列化操作，你可以自定义序列化机制。

例如在配置文件中设置为使用`JSON`序列化：

```php
'serialize'    =>    ['json_encode', 'json_decode'],
```

> 尽量避免把对象保存到`Session`会话

## 基础用法

### 赋值

```php
Session::set('name', 'thinkphp');
```

### 判断是否存在

```php
Session::has('name');
```

### 取值

```php
// 如果值不存在，返回null
Session::get('name');
// 如果值不存在，返回空字符串
Session::get('name', '');
// 获取全部数据
Session::all();
```

### 删除

```php
Session::delete('name');
```

### 取值并删除

```php
// 取值并删除
Session::pull('name');
```

如果name的值不存在，返回`Null`。

### 清空

```php
Session::clear();
```

### 闪存数据，下次请求之前有效

```php
// 设置session 并且在下一次请求之前有效
Session::flash('name','value');
```

### 提前清除当前请求有效的数据

```php
// 清除当前请求有效的session
Session::flush();
```

> 注意，`Session`写入数据的操作会在请求结束的时候统一进行本地化存储，所以不要在写入`Session`数据之后使用exit等中断操作，可能会导致`Session`没有正常写入。

## 多级数组

支持`session`的多级数组操作，例如：

```php
// 赋值
Session::set('name.item','thinkphp');
// 判断是否赋值
Session::has('name.item');
// 取值
Session::get('name.item');
// 删除
Session::delete('name.item');
```

其中`set`和`delete`方法只能支持二级数组，其他方法支持任意级数组操作。

## 助手函数

系统也提供了助手函数`session`完成相同的功能，例如：

```php
// 赋值
session('name', 'thinkphp');
// 判断是否赋值
session('?name');
// 取值
session('name');
// 删除
session('name', null);
// 清除session
session(null);
```

## Request对象中读取Session

可以在Request对象中读取Session数据

```php
public function index(Request $request) {
    // 读取某个session数据
    $request->session('user.name', '');
    // 获取全部session数据
    $request->session();
}
```

但`Request`类中不支持`Session`写入操作。

## 应用独立会话

> 多应用情况下默认`Session`是跨应用的，也就是说多应用之间是共享会话数据的，如果不希望共享会话数据，可以给每个应用设置不同的前缀`prefix`。

如果是File类型的话，默认的`session`会话数据保存在`runtime/session`目录下面，你可以设置`path`改变存储路径。

## Session驱动

默认的`Session`驱动采用文件缓存方式记录，并且支持如下配置

| 参数 | 描述 |
| :--- | :--- |
| path | session保存路径 |
| data\_compress | 是否压缩数据 |
| gc\_divisor | GC回收概率 |
| gc\_probability | GC回收概率 |

除了文件类型之外，还可以支持直接使用缓存作为`Session`类型，例如：

```php
return [
    'type'       => 'cache',
    'store'      => 'redis',
    'prefix'     => 'think',
]
```

表示使用`redis`作为`session`类型。

要以上的配置生效，请确保缓存配置文件`cache.php`中的`stores`中已经添加了`redis`缓存配置，例如：

```php
return [
    'default'    =>    'file',
    'stores'    =>    [
        // 文件缓存
        'file'   =>  [
            // 驱动方式
            'type'   => 'file',
            // 设置不同的缓存保存目录
            'path'   => '../runtime/file/',
        ],  
        // redis缓存
        'redis'   =>  [
            // 驱动方式
            'type'   => 'redis',
            // 服务器地址
            'host'       => '127.0.0.1',
        ],  
    ],
];
```

## 自定义驱动

如果需要自定义`Session`驱动，你的驱动类必须实现`think\contract\SessionHandlerInterface`接口，包含了三个方法。

```php
interface SessionHandlerInterface
{
    public function read(string $sessionId): string;
    public function delete(string $sessionId): bool;
    public function write(string $sessionId, string $data): bool;
}
```

`read`方法是在调用`Session::start()`的时候执行，并且只会执行一次。  
`write`方法是在本地化会话数据的时候执行（调用`Session::save()`方法），系统会在每次请求结束的时候自动执行。  
`delete`方法是在销毁会话的时候执行（调用`Session::destroy()`方法）。



