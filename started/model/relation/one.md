## 一对一关联

### 关联定义

定义一对一关联，例如，一个用户都有一个个人资料，我们定义`User`模型如下：

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

`hasOne`方法的参数包括：

> ### hasOne\('关联模型类名', '外键', '主键'\);

除了关联模型外，其它参数都是可选。

* **关联模型**（必须）：关联模型类名
* **外键**：默认的外键规则是当前模型名（不含命名空间，下同）+`_id`，例如`user_id`
* **主键**：当前模型主键，默认会自动获取也可以指定传入

一对一关联定义的时候还支持额外的方法，包括：

| 方法名 | 描述 |
| :--- | :--- |
| bind | 绑定关联属性到父模型 |
| joinType | JOIN方式查询的JOIN方式，默认为`INNER` |

> 如果使用了JOIN方式的关联查询方式，你可以在额外的查询条件中使用关联对象名（不含命名空间）作为表的别名。

### 关联查询

定义好关联之后，就可以使用下面的方法获取关联数据：

```php
$user = User::find(1);
// 输出Profile关联模型的email属性
echo $user->profile->email;
```

默认情况下， 我们使用的是`user_id`作为外键关联，如果不是的话则需要在关联定义的时候指定，例如：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    public function profile()
    {
        return $this->hasOne(Profile::class, 'uid');
    }
}
```

> 有一点需要注意的是，关联方法的命名规范是驼峰法，而关联属性则一般是小写+下划线的方式，系统在获取的时候会自动转换对应，读取`user_profile`关联属性则对应的关联方法应该是`userProfile`。

### 根据关联数据查询

可以根据关联条件来查询当前模型对象数据，例如：

```php
// 查询用户昵称是think的用户
// 注意第一个参数是关联方法名（不是关联模型名）
$users = User::hasWhere('profile', ['nickname'=>'think'])->select();

// 可以使用闭包查询
$users = User::hasWhere('profile', function($query) {
	$query->where('nickname', 'like', 'think%');
})->select();
```

### 预载入查询

可以使用预载入查询解决典型的`N+1`查询问题，使用：

```php
$users = User::with('profile')->select();
foreach ($users as $user) {
	echo $user->profile->name;
}
```

上面的代码使用的是`IN`查询，只会产生2条SQL查询。

如果要对关联模型进行约束，可以使用闭包的方式。

```php
$users = User::with(['profile'	=> function($query) {
	$query->field('id,user_id,name,email');
}])->select();
foreach ($users as $user) {
	echo $user->profile->name;
}
```

`with`方法可以传入数组，表示同时对多个关联模型（支持不同的关联类型）进行预载入查询。

```php
$users = User::with(['profile','book'])->select();
foreach ($users as $user) {
	echo $user->profile->name;
    foreach($user->book as $book) {
    	echo $book->name;
    }
}
```

如果需要使用`JOIN`方式的查询，直接使用`withJoin`方法，如下：

```php
$users = User::withJoin('profile')->select();
foreach ($users as $user) {
	echo $user->profile->name;
}
```

`withJoin`方法默认使用的是`INNER JOIN`方式，如果需要使用其它的，可以改成

```php
$users = User::withJoin('profile', 'LEFT')->select();
foreach ($users as $user) {
	echo $user->profile->name;
}
```

需要注意的是`withJoin`方式不支持嵌套关联，通常你可以直接传入多个需要关联的模型。

如果需要约束关联字段，可以使用下面的简便方法。

```php
$users = User::withJoin([
	'profile'	=>	['user_id', 'name', 'email']
])->select();
foreach ($users as $user) {
	echo $user->profile->name;
}
```

### 关联保存

```php
$user = User::find(1);
// 如果还没有关联数据 则进行新增
$user->profile()->save(['email' => 'thinkphp']);
```

系统会自动把当前模型的主键传入`Profile`模型。

和新增一样使用`save`方法进行更新关联数据。

```php
$user = User::find(1);
$user->profile->email = 'thinkphp';
$user->profile->save();
// 或者
$user->profile->save(['email' => 'thinkphp']);
```

### 定义相对关联

我们可以在`Profile`模型中定义一个相对的关联关系，例如：

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

`belongsTo`的参数包括：

> ### belongsTo\('关联模型','外键', '关联主键'\);

除了关联模型外，其它参数都是可选。

* **关联模型**（必须）：关联模型类名
* **外键**：当前模型外键，默认的外键名规则是关联模型名+`_id`
* **关联主键**：关联模型主键，一般会自动获取也可以指定传入

默认的关联外键是`user_id`，如果不是，需要在第二个参数定义

```php
<?php
namespace app\model;

use think\Model;

class Profile extends Model 
{
    public function user()
    {
        return $this->belongsTo(User::class, 'uid');
    }
}
```

我们就可以根据档案资料来获取用户模型的信息

```php
$profile = Profile::find(1);
// 输出User关联模型的属性
echo $profile->user->account;
```

## 绑定属性到父模型

可以在定义关联的时候使用`bind`方法绑定属性到父模型，例如：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    public function profile()
    {
        return $this->hasOne(Profile::class, 'uid')->bind(['nickname', 'email']);
    }
}
```

或者指定绑定属性别名

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    public function profile()
    {
        return $this->hasOne(Profile::class, 'uid')->bind([
            'email',
            'truename'	=> 'nickname',
        ]);
    }
}
```

然后使用关联预载入查询的时候，可以使用

```php
$user = User::with('profile')->find(1);
// 直接输出Profile关联模型的绑定属性
echo $user->email;
echo $user->truename;
```

绑定关联模型的属性支持读取器。

> 如果不是预载入查询，请使用模型的`appendRelationAttr`方法追加属性。

也可以使用动态绑定关联属性，可以使用

```php
$user = User::find(1)->bindAttr('profile',['email','nickname']);
// 输出Profile关联模型的email属性
echo $user->email;
echo $user->nickname;
```

同样支持指定属性别名

```php
$user = User::find(1)->bindAttr('profile',[
    'email',
    'truename'	=> 'nickname',
]);
// 输出Profile关联模型的email属性
echo $user->email;
echo $user->truename;
```

## 关联自动写入

我们可以使用`together`方法更方便的进行关联自动写入操作。

写入

```php
$blog = new Blog;
$blog->name = 'thinkphp';
$blog->title = 'ThinkPHP5关联实例';
$content = new Content;
$content->data = '实例内容';
$blog->content = $content;
$blog->together(['content'])->save();
```

如果绑定了子模型的属性到当前模型，可以指定子模型的属性

```php
$blog = new Blog;
$blog->name = 'thinkphp';
$blog->title = 'ThinkPHP5关联实例';
$blog->content = '实例内容';
// title和content是子模型的属性
$blog->together(['content' => ['title', 'content']])->save();
```

更新

```
// 查询
$blog = Blog::find(1);
$blog->title = '更改标题';
$blog->content->data = '更新内容';
// 更新当前模型及关联模型
$blog->together(['content'])->save();
```

删除

```php
// 查询
$blog = Blog::with('content')->find(1);
// 删除当前及关联模型
$blog->together(['content'])->delete();
```

  


