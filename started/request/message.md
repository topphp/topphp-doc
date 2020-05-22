## 请求信息

`Request`对象支持获取当前的请求信息，包括：

| 方法 | 含义 |
| :--- | :--- |
| `host` | 当前访问域名或者IP |
| `scheme` | 当前访问协议 |
| `port` | 当前访问的端口 |
| `remotePort` | 当前请求的REMOTE\_PORT |
| `protocol` | 当前请求的SERVER\_PROTOCOL |
| `contentType` | 当前请求的CONTENT\_TYPE |
| `domain` | 当前包含协议的域名 |
| `subDomain` | 当前访问的子域名 |
| `panDomain` | 当前访问的泛域名 |
| `rootDomain` | 当前访问的根域名 |
| `url` | 当前完整URL |
| `baseUrl` | 当前URL（不含QUERY\_STRING） |
| `query` | 当前请求的QUERY\_STRING参数 |
| `baseFile` | 当前执行的文件 |
| `root` | URL访问根地址 |
| `rootUrl` | URL访问根目录 |
| `pathinfo` | 当前请求URL的pathinfo信息（含URL后缀） |
| `ext` | 当前URL的访问后缀 |
| `time` | 获取当前请求的时间 |
| `type` | 当前请求的资源类型 |
| `method` | 当前请求类型 |
| `rule` | 当前请求的路由对象实例 |

对于上面的这些请求方法，一般调用无需任何参数，但某些方法可以传入`true`参数，表示获取带域名的完整地址，例如：

```php
use think\facade\Request;
// 获取完整URL地址 不带域名
Request::url();
// 获取完整URL地址 包含域名
Request::url(true);
// 获取当前URL（不含QUERY_STRING） 不带域名
Request::baseFile();
// 获取当前URL（不含QUERY_STRING） 包含域名
Request::baseFile(true);
// 获取URL访问根地址 不带域名
Request::root();
// 获取URL访问根地址 包含域名
Request::root(true);
```

> 注意`domain`方法的值本身就包含协议和域名

## 获取当前控制器/操作

可以通过请求对象获取当前请求的控制器/操作名。

| 方法 | 含义 |
| :--- | :--- |
| `controller` | 当前请求的控制器名 |
| `action` | 当前请求的操作名 |

获取当前控制器

```php
Request::controller();
```

返回的是控制器的驼峰形式（首字母大写），和控制器类名保持一致（不含后缀）。

如果需要返回小写可以使用

```php
Request::controller(true);
```

如果要返回小写+下划线的方式，可以使用

```php
parse_name(Request::controller());
```

获取当前操作

```php
Request::action();
```

返回的是当前操作方法的实际名称，如果需要返回小写可以使用

```php
Request::action(true);
```

如果要返回小写+下划线的方式，可以使用

```php
parse_name(Request::action());
```

如果使用了多应用模式，可以通过下面的方法来获取当前应用

```php
app('http')->getName();
```



