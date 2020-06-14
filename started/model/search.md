## 搜索器

搜索器的作用是用于封装字段（或者搜索标识）的查询条件表达式，一个搜索器对应一个特殊的方法（该方法必须是`public`类型），方法命名规范为：

> ### search`FieldName`Attr

`FieldName`为数据表字段的驼峰转换，搜索器仅在调用`withSearch`方法的时候触发。

搜索器的场景包括：

* 限制和规范表单的搜索条件；
* 预定义查询条件简化查询；

例如，我们需要给User模型定义`name`字段和时间字段的搜索器，可以使用：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    public function searchNameAttr($query, $value, $data)
    {
        $query->where('name','like', $value . '%');
    }

    public function searchCreateTimeAttr($query, $value, $data)
    {
        $query->whereBetweenTime('create_time', $value[0], $value[1]);
    }    
}
```

然后，我们可以使用下面的查询

```php
User::withSearch(['name','create_time'], [
        'name'        =>    'think',
        'create_time' =>    ['2018-8-1','2018-8-5'],
        'status'      =>    1
    ])
    ->select();
```

最终生成的SQL语句类似于

```php
SELECT * FROM `think_user` WHERE  `name` LIKE 'think%' AND `create_time` BETWEEN '2018-08-01 00:00:00' AND '2018-08-05 00:00:00'
```

可以看到查询条件中并没有`status`字段的数据，因此可以很好的避免表单的非法查询条件传入，在这个示例中仅能使用`name`和`create_time`条件进行查询。

> 事实上，除了在搜索器中使用查询表达式外，还可以使用其它的任何查询构造器以及链式操作。

例如，你需要通过表单定义的排序字段进行搜索结果的排序，可以使用

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    public function searchNameAttr($query, $value, $data)
    {
        $query->where('name','like', $value . '%');
        if (isset($data['sort'])) {
            $query->order($data['sort']);
        }        
    }

    public function searchCreateTimeAttr($query, $value, $data)
    {
        $query->whereBetweenTime('create_time', $value[0], $value[1]);
    }      
}
```

然后，我们可以使用下面的查询

```php
User::withSearch(['name','create_time', 'status'], [
    'name'        =>    'think',
        'create_time'    =>    ['2018-8-1','2018-8-5'],
        'status'    =>    1,
        'sort'        =>    ['status'=>'desc'],
    ])
    ->select();
```

最终查询的SQL可能是

```php
SELECT * FROM `think_user` WHERE  `name` LIKE 'think%' AND `create_time` BETWEEN '2018-08-01 00:00:00' AND '2018-08-05 00:00:00' ORDER BY `status` DESC
```

你可以给搜索器定义字段别名，例如：

```php
User::withSearch(['name'=>'nickname','create_time', 'status'], [
    'nickname'    =>    'think',
        'create_time'    =>    ['2018-8-1','2018-8-5'],
        'status'    =>    1,
        'sort'        =>    ['status'=>'desc'],
    ])
    ->select();
```

> 搜索器通常会和查询范围进行比较，搜索器无论定义了多少，只需要一次调用，查询范围如果需要组合查询的时候就需要多次调用。



