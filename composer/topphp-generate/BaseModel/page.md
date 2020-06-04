## 分页及其他内置方法

### 分页

如果你需要构造`ThinkPHP`的分页数据，模型内部提供`protected`属性的`getPaginateConfig`方法：

```php
$pageLimit = 10;// 每页显示条数
$pageConfig = $this->getPaginateConfig($pageLimit);
```

上面的方法将会返回`ThinkPHP`模型的`paginate`方法所需要的分页配置数组：

```php
array (
    "list_rows" => 10,
    "var_page" => "page",
    "query" => []
)
```

默认的请求分页参数名为`page`，无论是`post`请求，还是`get`请求，只要在请求参数中加入`page`键，即表示当前页数在第几页，模型会自动获取该参数，查询时会自动定位到指定页数据。如：

```php
namespace app\index\model;

use app\model\entity\User;

class UserDao extends User
{
    public function getUserList()
    {
        $pageLimit = 10;// 每页显示条数
        $pageConfig = $this->getPaginateConfig($pageLimit);
        return $this->paginate($pageConfig)->toArray();
    }
}
```

当我们访问如`https://www.domain.com/index/user/list?page=2`这样的地址时，将会获取到用户列表的第二页数据。

> `getPaginateConfig`的每页显示条数参数`$pageLimit`默认为`0`，表示会自动根据`TopPHP`骨架的`PaginateEnum`枚举类配置自动获取。

你可以像上面的方式手动传入每页显示条数，但我们更推荐使用枚举类来管理分页配置，例如我们在`index/User/list`操作中直接像如下方式使用：

```php
namespace app\index\model;

use app\model\entity\User;

class UserDao extends User
{
    public function getUserList()
    {
        $pageConfig = $this->getPaginateConfig();
        return $this->paginate($pageConfig)->toArray();
    }
}

...

namespace app\index\controller;

use app\index\model\UserDao;

class User extends Base
{
    public function list()
    {
        $user = new UserDao;
        dump($user->getUserList());
    }
}
```

打开`app\common\enumerate\PaginateEnum`枚举类，可以看到`ONE_PAGE_LIMIT`静态常量：

```php
// 默认每页显示条数 default
const ONE_PAGE_LIMIT = [
    "default"            => 15,// BaseModel全局默认分页每页显示条数

    // 注意：如果是多层级或多版本控制器，请务必按照规范填写全控制器，如 api/v1.Index/index 或 index/layered.Index/index，否则将不生效，会自动使用默认的

    // 应用名/控制器名/操作名 =》 每页显示条数（支持通配符设置全控制器通用--应用名/控制器名/*）

    //--- api 应用 ---//
    "api/v1.Index/index" => 10, // index
    "api/v2.Index/index" => 10, // index

];
```

我们可以看到`TopPHP`骨架的示例说明，在其中加入`index/User/list`参数值为`5`：

```php
// 默认每页显示条数 default
const ONE_PAGE_LIMIT = [
    "default"         => 15,
    "index/User/list" => 5 
];
```

访问`index/User/list`的时候就是每页显示`5`条数据了。

> 配置`ONE_PAGE_LIMIT`参数需要注意控制器和方法名，请严格按照**开发规范**的命名规则命名，例如：控制器类的命名采用驼峰法（首字母大写），多层级或多版本控制器需按照上面的`api`应用配置方式配置；操作方法名使用驼峰法（首字母小写）。

`ONE_PAGE_LIMIT`参数支持通配符配置：

```php
// 默认每页显示条数 default
const ONE_PAGE_LIMIT = [
    "default"      => 15,
    "index/User/*" => 5 
];
```

像上面的方式，整个`User`控制器将会使用每页显示`5`条数据的分页规则。

> `getPaginateConfig`的每页显示条数参数优先级规则为：优先获取手动传入的数值，如果不传或传`0`将会自动获取`PaginateEnum`枚举类的配置，如果都没有配置，将会使用默认值`15`。

### 数组分页

`BaseModel`提供`protected`属性的`dataPage`数组分页方法，主要针对小数据量的分页场景，如你有一个固定数量的数据集，你需要将该数据进行自定义的拆分处理：

```php
$list = [
    ["id" => 1, "city" => "北京"],
    ["id" => 2, "city" => "天津"],
    ["id" => 3, "city" => "河北"],
    ...
];
$page = 1;// 当前页
$limit = 10;// 每页显示10条
$this->dataPage($list, $page, $limit);
```

`dataPage`会根据`$page`页码，在全部数据`$list`中按照`$limit`的每页显示数量返回指定页的数据。

### 返回数组维度

`BaseModel`提供`protected`属性的`arrayLevel`获取数组维度的方法，该方法返回传入的数组是几维数组`int`值。

```php
$list = [
    ["id" => 1, "city" => "北京"],
    ["id" => 2, "city" => "天津"],
    ["id" => 3, "city" => "河北"],
    ...
];
$this->arrayLevel($list);
```

上面将会返回`int`型的数字`2`，表示`$list`是个二维数组。

### 获取资源数据指定列的数组

`BaseModel`提供`protected`属性的`getSourceColumn`获取查询结果集中指定列的数据：

```php
$list = $this->where("id", ">", 10)->select();
$userNameList = $this->getSourceColumn($list, "username");
```

上面将会返回`id`大于`10`的用户名字段值组成的数组，效果与`ThinkPHP`的`column`查询相似，提供这个方法主要是为了增加业务处理数据的灵活性。

> `getSourceColumn`方法第一个参数必须是二维数组，第二个参数为第二维的数据`key`名。

### 获取数据表字段结构信息

`BaseModel`提供`public`属性的`getTableFieldName`用于获取数据模型字段信息：

```php
$fields = $this->getTableFieldName();
```

`$fields`将会返回类似如下字段名数组：

```php
array (
  0 => "id"
  1 => "username"
  2 => "password"
  3 => "email"
  4 => "tel"
  5 => "login_time"
  6 => "login_ip"
  7 => "create_time"
  8 => "update_time"
  9 => "delete_time"
)
```