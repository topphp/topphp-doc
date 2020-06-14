## 多对多关联

## 关联定义

例如，我们的用户和角色就是一种多对多的关系，我们在`User`模型定义如下：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    public function roles()
    {
        return $this->belongsToMany(Role::class, 'access');
    }
}
```

`belongsToMany`方法的参数如下：

> ### belongsToMany\('关联模型','中间表','外键','关联键'\);

* **关联模型**（必须）：关联模型类名
* **中间表**：默认规则是当前模型名+`_`+关联模型名 （可以指定模型名）
* **外键**：中间表的当前模型外键，默认的外键名规则是关联模型名+`_id`
* **关联键**：中间表的当前模型关联键名，默认规则是当前模型名+`_id`

中间表名无需添加表前缀，并支持定义中间表模型，例如：

```php
    public function roles()
    {
        return $this->belongsToMany(Role::class, Access::class);
    }
```

中间表模型类必须继承`think\model\Pivot`，例如：

```php
<?php
namespace app\model;

use think\model\Pivot;

class Access extends Pivot
{
    protected $autoWriteTimestamp = true;
}
```

中间表模型的基类`Pivot`默认关闭了时间戳自动写入，上面的中间表模型则开启了时间戳字段自动写入。

### 关联查询

我们可以通过下面的方式获取关联数据

```php
$user = User::find(1);
// 获取用户的所有角色
$roles = $user->roles;
foreach ($roles as $role) {
	// 输出用户的角色名
	echo $role->name;
    // 获取中间表模型
    dump($role->pivot);
}
```

### 关联新增

```php
$user = User::find(1);
// 给用户增加管理员权限 会自动写入角色表和中间表数据
$user->roles()->save(['name'=>'管理员']);
// 批量授权
$user->roles()->saveAll([
    ['name'=>'管理员'],
    ['name'=>'操作员'],
]);
```

只新增中间表数据（角色已经提前创建完成），可以使用

```php
$user = User::find(1);
// 仅增加管理员权限（假设管理员的角色ID是1）
$user->roles()->save(1);
// 或者
$role = Role::find(1);
$user->roles()->save($role);
// 批量增加关联数据
$user->roles()->saveAll([1,2,3]);
```

单独更新中间表数据，可以使用：

```php
$user = User::find(1);
// 增加关联的中间表数据
$user->roles()->attach(1);
// 传入中间表的额外属性
$user->roles()->attach(1,['remark'=>'test']);
// 删除中间表数据
$user->roles()->detach([1,2,3]);
```

> `attach`方法的返回值是一个`Pivot`对象实例，如果是附加多个关联数据，则返回`Pivot`对象实例的数组。

### 定义相对的关联

我们可以在`Role`模型中定义一个相对的关联关系，例如：

```php
<?php
namespace app\model;

use think\Model;

class Role extends Model 
{
    public function users()
    {
        return $this->belongsToMany(User::class, Access::class);
    }
}
```



