## 查询所有

### selectAll 查询

> selectAll \( '查询条件', '排除字段', '\[ and \]是否or查询', '\[ false \]是否返回model对象' \);

```php
$user = new UserDao;
$where = [
    'name' => 'topphp'
];
$user->selectAll($where);
```

如果你需要排除某个字段，除了设置永久隐藏`$hidden`属性外还可以使用`selectAll`的第二个参数进行排除字段处理：

```php
$user = new UserDao;
$where = [
    'name' => 'topphp'
];
$user->selectAll($where, "password");
```

> `selectAll`的第二个参数`$withoutField`支持数组或字符串`field1,field2`形式。排除字段和隐藏字段的过滤方式不一样。

**注意：使用了`$withoutField`参数进行排除字段，那么查询出来的数据，使用`getModelData`就也获取不到被排除的字段信息了。**

之所以`selectAll`方法的第二个参数设置的是`排除字段`而不是`筛选字段`，是因为大多数情况下，我们都是查询数据表所有的数据，可能某些文本字段的值查询非常耗内存，查询时又不需要这样的数据，所以需要做排除处理。

设置第四个参数`$isModel`的目的是，往往我们的查询可能不仅限于直接查询出来，可能还需要其他的链式操作，所以提供直接返回`model`对象的方式，如我们还需要排序和一些更复杂的链式操作：

```php
$user = new UserDao;
$ids = [1, 3, 9];
$where = [
    ["id", "in", $ids],
    ["id", ">", 10]
];
$user->selectAll($where, [], "or", true)
->order("id", "desc")->select()
->each(function ($item) {
     if ($item['id'] == 9) {
         $item['username'] = "topphp";
     }
})->toArray();
```

> 如果你使用了第四个参数返回模型对象，那么当前查询的整个模式将进入`ThinkPHP`的链式查询操作模式。

如你需要筛选字段，而非排除字段，可以：

```php
$user = new UserDao;
$ids = [1, 3, 9];
$where = [
    ["id", "in", $ids],
    ["id", ">", 10]
];
$user->selectAll($where, [], "or", true)
->field(["id","username","create_time"])
->select()->toArray();
```

> 排除字段的优先级高于筛选字段，如果你既设置了排除字段又设置了筛选字段，返回将以排除字段为主。

如你需要分页，也可以在模型中按照如下方式使用：

```php
namespace app\index\model;

use app\model\entity\User;

class UserDao extends User
{
    public function getUserList()
    {
        $pageConfig = $this->getPaginateConfig();
        $withoutField = ["password", "delete_time"];
        return $this->selectAll("*", $withoutField, "and", true)->paginate($pageConfig)->toArray();
    }
}
```

其中`where`条件的`*`表示查询所有，所有快捷方法的`where`都不支持直接传空数组来识别为查询所有。

### selectSort 排序查询

> selectSort \( '查询条件', '查询排序', '\[ and \]是否or查询', '\[ * \]Limit筛选', '\[ false \]是否返回model对象' \);

如果你有排序的需求，我们更推荐直接使用`selectSort`方法，而非`selectAll`去链式操作：

```php
$user = new UserDao;
$ids = [1, 3, 9];
$where = [
    ["id", "in", $ids],
    ["id", ">", 10]
];
$order = ["create_time" => "desc"];
$user->selectSort($where, $order, "or");
```

`$order`参数多个字段的数组形式的排序可以如下定义：

```php
$order = ["score", "create_time" => "desc"];
```

生成类似如下SQL语句：

```php
SELECT * FROM `topphp_user` WHERE ( `id` IN (1,3,9) OR `id` > 10 ) AND ( `delete_time` IS NULL OR `delete_time` = 0 ) ORDER BY `score`,`create_time` DESC
```

如果都是升序：

```php
$order = ["score", "create_time"];
```

> `$order`参数如果没有指定`desc`或者`asc`排序规则的话，默认为`asc`。

如果你想使用原生的sql排序可以直接传入原生sql字符串：

```php
$order = "score,create_time desc";
```

> 如果你需要在`$order`参数中使用`mysql`函数的话，必须使用字符串的方式：

```php
$order = "field(name,'thinkphp','topphp','onethink')";
```

`selectSort`方法还支持`limit`筛选：

```php
$user = new UserDao;
$ids = [1, 3, 9];
$where = [
    ["id", "in", $ids],
    ["id", ">", 10]
];
$order = ["create_time" => "desc"];
$user->selectSort($where, $order, "or", 5);
```

上面将会获取`id`在`1`、`3`、`9`或者`id` > `10`的数据中按照创建时间降序排列的前`5`条数据。

> `$limit`筛选参数默认通配符`*`表示不进行`limit`筛选。`selectSort`第五个参数`$isModel`同样与`selectAll`的返回`model`对象参数一样可以为`true`切换`ThinkPHP`的链式操作。

```php
namespace app\index\model;

use app\model\entity\User;

class UserDao extends User
{
    public function getUserList()
    {
        $pageConfig = $this->getPaginateConfig();
        $order = ["create_time" => "desc"];
        return $this->selectSort("*", $order, "and", "*", true)->paginate($pageConfig)->toArray();
    }
}
```

上面的代码表示查询所有数据，按照创建时间降序排列进行分页。不过我们同样不推荐直接链式操作这样分页，因为我们还提供专门应对分页的方法`selectList`。

### selectList 查询分页

> selectList \( '查询条件', '查询排序', '\[ null \]each回调', '\[ 0 \]每页显示条数', '\[ and \]是否or查询' \);

如果你在`TopPHP`骨架的`PaginateEnum`枚举类配置了分页配置，那么可以直接通过下面的方式直接获取分页数据：

```php
$user = new UserDao;
$user->selectList("*");
```

如果你需要隐藏哪个字段，直接通过模型`Dao`的隐藏字段`$hidden`属性设置即可，`selectList`方法返回的数据将不包含隐藏字段信息。关于分页`PaginateEnum`枚举类如何配置可以查看[`分页及其他`](/composer/topphp-generate/BaseModel/page.md)模块。

如果你有`where`查询条件，可以直接传入查询条件：

```php
$user = new UserDao;
$where = ["id", ">", 10];
$user->selectList($where);
```

如果你有排序条件，可以直接传入排序条件：

```php
$user = new UserDao;
$where = ["id", ">", 10];
$order = ["create_time" => "desc"];
$user->selectList($where, $order);
```

查询条件和查询排序两个参数就不做详细赘述了，前面已经说明过了，来看一下第三个参数`$each`回调：

```php
$user = new UserDao;
$each = function ($item) {
    if ($item["id"] == 1) {
        $item["username"] = "topphp";
        $item["is_super_user"] = 1;
    }
};
$user->selectList("*", [], $each);
```

上面将会查询出所有的分页数据，并将`id`为`1`的数据`username`字段改为`topphp`，同时新增一个数据表不存在的字段`is_super_user`值为`1`。