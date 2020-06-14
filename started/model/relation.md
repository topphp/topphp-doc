## 模型关联

通过模型关联操作把数据表的关联关系对象化，解决了大部分常用的关联场景，封装的关联操作比起常规的数据库联表操作更加智能和高效，并且直观。

> 避免在模型内部使用复杂的`join`查询和视图查询。

从面向对象的角度来看关联的话，模型的关联其实应该是模型的某个属性，比如用户的档案关联，就应该是下面的情况：

```php
// 获取用户模型实例
$user = User::find(1);
// 获取用户的档案
$user->profile;
// 获取用户的档案中的手机资料
$user->profile->mobile;
```

为了更方便和灵活的定义模型的关联关系，框架选择了方法定义而不是属性定义的方式，每个**关联属性**其实是对应了一个模型的（关联）方法，这个关联属性和模型的数据一样是动态的，并非模型类的实体属性。

例如上面的关联属性就是在`User`模型类中定义了一个`profile`方法（`mobile`属性是`Profile`模型的属性）：

```php
<?php

namespace app\model;

use think\Model;

class User extends Model
{
	public function profile()
    {
    	return $this->hasOne(Profile::class);
    }
}
```

> 一个模型可以定义多个不同的关联，增加不同的关联方法即可

同时，我们必须定义一个`Profile`模型（即使是一个空模型）。

```php
<?php

namespace app\model;

use think\Model;

class Profile extends Model
{
}
```

关联方法返回的是不同的关联对象，例如这里的`profile`方法返回的是一个`HasOne`关联对象（`think\model\relation\HasOne`）实例。

当我们访问`User`模型对象实例的`profile`属性的时候，其实就是调用了`profile`方法来完成关联查询。

按照`PSR-2`规范，模型的方法名都是驼峰命名的，所以系统做了一个兼容处理，如果我们定义了一个`userProfile`的关联方法的时候，在获取关联属性的时候，下面两种方式都是有效的：

```php
$user->userProfile;
$user->user_profile; // 建议使用
```

> 推荐关联属性统一使用后者，和数据表的字段命名规范一致，因此在很多时候系统自动获取关联属性的时候采用的也是后者。

可以简单的理解为关联定义就是在模型类中添加一个方法（注意不要和模型的对象属性以及其它业务逻辑方法冲突），一般情况下无需任何参数，并在方法中指定一种关联关系，比如上面的`hasOne`关联关系，`6.0`版本支持的关联关系包括下面8种，后面会给大家陆续介绍：

| 模型方法 | 关联类型 |
| :--- | :--- |
| `hasOne` | 一对一 |
| `belongsTo` | 一对一 |
| `hasMany` | 一对多 |
| `hasOneThrough` | 远程一对一 |
| `hasManyThrough` | 远程一对多 |
| `belongsToMany` | 多对多 |
| `morphMany` | 多态一对多 |
| `morphOne` | 多态一对一 |
| `morphTo` | 多态 |

关联方法的第一个参数就是要关联的模型名称，也就是说当前模型的关联模型必须也是已经定义好的一个模型。

也可以使用完整命名空间定义，例如：

```php
<?php

namespace app\model;

use think\Model;

class User extends Model
{
	public function profile()
    {
    	return $this->hasOne(Profile::class);
    }
}
```

两个模型之间因为参照模型的不同就会产生相对的但不一定相同的关联关系，并且相对的关联关系只有在需要调用的时候才需要定义，下面是每个关联类型的相对关联关系对照：

| 类型 | 关联关系 | 相对的关联关系 |
| :--- | :--- | :--- |
| 一对一 | `hasOne` | `belongsTo` |
| 一对多 | `hasMany` | `belongsTo` |
| 多对多 | `belongsToMany` | `belongsToMany` |
| 远程一对多 | `hasManyThrough` | 不支持 |
| 多态一对一 | `morphOne` | `morphTo` |
| 多态一对多 | `morphMany` | `morphTo` |

例如，`Profile`模型中就可以定义一个相对的关联关系。

```php
<?php

namespace app\model;

use think\Model;

class Profile extends Model
{
	public function user()
    {
    	return $this->belongsTo(User::class);
    }
}
```

在进行关联查询的时候，也是类似，只是当前模型不同。

```php
// 获取档案实例
$profile = Profile::find(1);
// 获取档案所属的用户名称
echo $profile->user->name;
```

如果是数据集查询的话，关联获取的用法如下：

```php
// 获取档案实例
$profiles = Profile::where('id', '>', 1)->select();
foreach($profiles as $profile) {
	// 获取档案所属的用户名称
	echo $profile->user->name;
}
```

如果你需要对关联模型进行更多的查询约束，可以在关联方法的定义方法后面追加额外的查询链式方法（但切忌不要滥用，并且不要使用实际的查询方法），例如：

```php
<?php

namespace app\model;

use think\Model;

class User extends Model
{
	public function book()
    {
    	return $this->hasMany(Book::class)->order('pub_time');
    }
}
```

> 模型关联支持调用模型的方法

> 具体不同的关联关系的详细使用，请继续参考后面的内容。



