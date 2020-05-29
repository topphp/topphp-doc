## 模型字段

模型的数据字段和对应数据表的字段是对应的，默认会自动获取（包括字段类型），但自动获取会导致增加一次查询，因此你可以在模型中明确定义字段信息避免多一次查询的开销。

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    // 设置字段信息
    protected $schema = [
        'id'          => 'int',
        'name'        => 'string',
        'status'      => 'int',
        'score'       => 'float',
        'create_time' => 'datetime',
        'update_time' => 'datetime',
    ];
}
```

> 字段类型的定义可以使用PHP类型或者数据库的字段类型都可以，字段类型定义的作用主要用于查询的参数自动绑定类型。

> 时间字段尽量采用实际的数据库类型定义，便于时间查询的字段自动识别。如果是`json`类型直接定义为`json`即可。

如果你没有定义`schema`属性的话，可以在部署完成后运行如下指令。

```php
php think optimize:schema

```

运行后会自动生成数据表的字段信息缓存。使用命令行缓存的优势是Db类的查询仍然有效。

> `TopPHP`骨架设计结构已经把上述情况考虑进去，所以默认情况下，你无需手动定义模型字段，直接使用[`topphp/topphp-generate`](/composer/topphp-generate.md)组件自动创建数据表实体类即可。

## 字段类型

`schema`属性一旦定义，就必须定义完整的数据表字段类型。  
如果你只希望对某个字段定义需要自动转换的类型，可以使用`type`属性，例如：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    // 设置字段自动转换类型
    protected $type = [
        'score'       => 'float',
    ];
}
```

`type`属性定义的不一定是实际的字段，也有可能是你的字段别名。

## 废弃字段

如果因为历史遗留问题 ，你的数据表存在很多的废弃字段，你可以在模型里面定义这些不再使用的字段。

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    // 设置废弃字段
    protected $disuse = [ 'status', 'type' ];
}
```

在查询和写入的时候会忽略定义的`status`和`type`废弃字段。

## 获取数据

在模型外部获取数据的方法如下

```php
$user = User::find(1);
echo $user->create_time;  
echo $user->name;
```

由于模型类实现了`ArrayAccess`接口，所以可以当成数组使用。

```php
$user = User::find(1);
echo $user['create_time'];  
echo $user['name'];
```

如果你是在模型内部获取数据的话，需要改成：

```php
$user = $this->find(1);
echo $user->getAttr('create_time');  
echo $user->getAttr('name');
```

否则可能会出现意想不到的错误。

## 模型赋值

可以使用下面的代码给模型对象赋值

```php
$user = new User();
$user->name = 'thinkphp';
$user->score = 100;
```

该方式赋值会自动执行模型的修改器，如果不希望执行修改器操作，可以使用

```php
$data['name'] = 'thinkphp';
$data['score'] = 100;
$user = new User($data);
```

或者使用

```php
$user = new User();
$data['name'] = 'thinkphp';
$data['score'] = 100;
$user->data($data);
```

`data`方法支持使用修改器

```php
$user = new User();
$data['name'] = 'thinkphp';
$data['score'] = 100;
$user->data($data, true);
```

如果需要对数据进行过滤，可以使用

```php
$user = new User();
$data['name'] = 'thinkphp';
$data['score'] = 100;
$user->data($data, true, ['name','score']);
```

表示只设置`data`数组的`name`和`score`数据。

## 严格区分字段大小写

默认情况下，你的模型数据名称和数据表字段应该保持严格一致，也就是说区分大小写。

```php
$user = User::find(1);
echo $user->create_time;  // 正确
echo $user->createTime;  // 错误
```

> 严格区分字段大小写的情况下，如果你的数据表字段是大写，模型获取的时候也必须使用大写。

如果你希望在获取模型数据的时候不区分大小写（前提是数据表的字段命名必须规范，即小写+下划线），可以设置模型的`strict`属性。

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    // 模型数据不区分大小写
    protected $strict = false,
}
```

你现在可以使用

```php
$user = User::find(1);
// 下面两种方式都有效
echo $user->createTime; 
echo $user->create_time; 
```



