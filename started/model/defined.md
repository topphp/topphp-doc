## 模型定义

定义一个模型类很简单，例如下面是一个单应用的`User`模型：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
}
```

> 请确保你已经在数据库配置文件中配置了数据库连接信息，如不清楚请参考数据库一章

模型会自动对应数据表，模型类的命名规则是除去表前缀的数据表名称，采用驼峰法命名，并且首字母大写，例如：

| 模型名 | 约定对应数据表（假设数据库的前缀定义是`think_`） |
| :--- | :--- |
| User | think\_user |
| UserType | think\_user\_type |

如果你的规则和上面的系统约定不符合，那么需要设置Model类的数据表名称属性，以确保能够找到对应的数据表。

> 模型自动对应的数据表名称都是遵循小写+下划线规范，如果你的表名有大写的情况，必须通过设置模型的`table`属性。

如果你希望给模型类添加后缀，需要设置`name`属性或者`table`属性。

```php
<?php
namespace app\model;

use think\Model;

class UserModel extends Model
{
    protected $name = 'user';
}
```

## 模型设置

默认主键为`id`，如果你没有使用`id`作为主键名，需要在模型中设置属性：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    protected $pk = 'uid';
}
```

如果你想指定数据表甚至数据库连接的话，可以使用：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    // 设置当前模型对应的完整数据表名称
    protected $table = 'think_user';
    
    // 设置当前模型的数据库连接
    protected $connection = 'db_config';
}
```

> `connection`属性使用用配置参数名（需要在数据库配置文件中的`connections`参数中添加对应标识）。

常用的模型设置属性包括（以下属性都不是必须设置）：

| 属性 | 描述 |
| :--- | :--- |
| name | 模型名（相当于不带数据表前后缀的表名，默认为当前模型类名） |
| table | 数据表名（默认自动获取） |
| suffix | 数据表后缀（默认为空） |
| pk | 主键名（默认为`id`） |
| connection | 数据库连接（默认读取数据库配置） |
| query | 模型使用的查询类名称 |
| field | 模型允许写入的字段列表（数组） |
| schema | 模型对应数据表字段及类型 |
| type | 模型需要自动转换的字段及类型 |
| strict | 是否严格区分字段大小写（默认为true） |
| disuse | 数据表废弃字段（数组） |

> 模型不支持对数据表的前缀单独设置，并且也不推荐使用数据表的前缀设计，应该用不同的库区分。当你的数据表没有前缀的时候，`name`和`table`属性的定义是没有区别的，定义任何一个即可。

## `TopPHP`模型定义

以上定义方式在`TopPHP`骨架下按照如下方式定义：

```php
<?php
declare(strict_types=1);

namespace app\index\model;

use app\model\entity\User;

class UserDao extends User
{
}
```

`app\model\entity\User`结构如下：

```php
<?php
declare(strict_types=1);

namespace app\model\entity;

use app\model\BaseModel;
use think\Model;

/**
 * @property int $id
 * @property string $account 登录账号
 * @property string $password 登录密码
 * @property string $username 用户名
 * @property string $tel 电话
 * @property string $create_time 添加时间
 * @property string $update_time 更新时间
 * @property string $delete_time 删除时间
 */
class User extends Model
{
    use BaseModel;

    protected $pk = 'id';

    protected $table = 'topphp_admin';

    protected $schema = [
        'id' => 'int',
        'account' => 'varchar',
        'password' => 'varchar',
        'username' => 'varchar',
        'tel' => 'varchar',
        'create_time' => 'timestamp',
        'update_time' => 'timestamp',
        'delete_time' => 'timestamp',
    ];
}
```

## 模型初始化

模型支持初始化，只需要定义`init`方法，例如：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model
{
    // 模型初始化
    protected static function init()
    {
        //TODO:初始化内容
    }
}
```

> `init`必须是静态方法，并且只在第一次实例化的时候执行，并且只会执行一次

## 模型操作

> 在模型中除了可以调用数据库类的方法之外（换句话说，**数据库的所有查询构造器方法模型中都可以支持**），可以定义自己的方法，所以也可以把模型看成是数据库的增强版。

模型的操作方法无需和数据库查询一样调用必须首先调用`table`或者`name`方法，因为模型会按照规则自动匹配对应的数据表，例如：

```php
Db::name('user')->where('id','>',10)->select();
```

改成模型操作的话就变成

```php
User::where('id','>',10)->select();
```

虽然看起来是相同的查询条件，但一个最明显的区别是**查询结果的类型**不同。第一种方式的查询结果是一个（二维）数组，而第二种方式的查询结果是包含了模型（集合）的数据集。不过，在大多数情况下，这二种返回类型的使用方式并无明显区别。

模型操作和数据库操作的另外一个显著区别是模型支持包括获取器、修改器、自动时间写入在内的一系列自动化操作和事件，简化了数据的存取操作，但随之而来的是性能有所下降（其实并没下降，而是自动帮你处理了一些原本需要手动处理的操作），后面会逐步领略到模型的这些特色功能。

## 动态切换后缀

新版模型增加了一个数据表后缀属性，可以用于多语言或者数据分表的模型查询，省去为多个相同结构的表定义多个模型的麻烦。

默认的数据表后缀可以在模型类里面直接定义`suffix`属性。

```php
<?php
namespace app\model;

use think\Model;

class Blog extends Model
{
    // 定义默认的表后缀（默认查询中文数据）
    protected $suffix = _cn';
}
```

> 你在模型里面定义的`name`和`table`属性无需包含后缀定义

模型提供了动态切换方法`suffix`和`setSuffix`，例如：

```php
// suffix方法用于静态查询
$blog = Blog::suffix('_en')->find();
$blog->name = 'test';
$blog->save();

// setSuffix用于动态设置
$blog = new Blog($data);
$blog->setSuffix('_en')->save();
```

## 模型方法依赖注入

如果你需要对模型的方法支持依赖注入，可以把模型的方法改成闭包的方式，例如，你需要对获取器方法增加依赖注入

```php
public function getTestFieldAttr($value,$data) {
    return $this->invoke(function(Request $request)  use($value,$data) {
        return $data['name'] . $request->action();
    });
}
```

不仅仅是获取器方法，在任何需要依赖注入的方法都可以改造为调用`invoke`方法的方式，`invoke`方法第二个参数用于传入需要调用的（数组）参数。

如果你需要直接调用某个已经定义的模型方法（假设已经使用了依赖注入），可以使用

```php
protected function bar($name, Request $request) {
    // ...
}

protected function invokeCall(){
    return $this->invoke('bar',['think']);
}
```



