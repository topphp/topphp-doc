## 模型输出

模型数据的模板输出可以直接把模型对象实例赋值给模板变量，在模板中可以直接输出，例如：

```php
<?php
namespace app\controller;

use app\model\User;
use think\facade\View;

class Index
{
    public function index()
    {
        $user = User::find(1);
        View::assign('user', $user);
        
        return View::fetch();
    }
}
```

在模板文件中可以使用

```php
{$user.name}
{$user.email}
```

> 模板中的模型数据输出一样会调用获取器。

## 数组转换

可以使用`toArray`方法将当前的模型实例输出为数组，例如：

```php
$user = User::find(1);
dump($user->toArray());
```

支持设置不输出的字段属性：

```php
$user = User::find(1);
dump($user->hidden(['create_time','update_time'])->toArray());
```

数组输出的字段值会经过获取器的处理，也可以支持追加其它获取器定义（不在数据表字段列表中）的字段，例如：

```php
$user = User::find(1);
dump($user->append(['status_text'])->toArray());
```

支持设置允许输出的属性，例如：

```php
$user = User::find(1);
dump($user->visible(['id','name','email'])->toArray());
```

对于数据集结果一样可以直接使用（包括`append`、`visible`和`hidden`方法）

```php
$list = User::select();
$list = $list->toArray();
```

可以在查询之前定义`hidden`/`visible`/`append`方法，例如：

```php
dump(User::where('id',10)->hidden(['create_time','update_time'])->append(['status_text'])->find()->toArray());
```

注意，必须要首先调用一次Db类的方法后才能调用`hidden`/`visible`/`append`方法。

### 追加关联属性

支持追加关联模型的属性到当前模型，例如：

```php
$user = User::find(1);
dump($user->append(['profile' => ['email', 'nickname']])->toArray());
```

`profile`是关联定义方法名，`email`和`nickname`是`Profile`模型的属性。

模型的`visible`、`hidden`和`append`方法支持关联属性操作，例如：

```php
$user = User::with('profile')->find(1);
// 隐藏profile关联属性的email属性
dump($user->hidden(['profile'=>['email']])->toArray());
// 或者使用
dump($user->hidden(['profile.email'])->toArray());
```

`hidden`、`visible`和`append`方法同样支持数据集对象。

## JSON序列化

可以调用模型的`toJson`方法进行`JSON`序列化，`toJson`方法的使用和`toArray`一样。

```php
$user = User::find(1);
echo $user->toJson();
```

可以设置需要隐藏的字段，例如：

```php
$user = User::find(1);
echo $user->hidden(['create_time','update_time'])->toJson();
```

或者追加其它的字段（该字段必须有定义获取器）：

```php
$user = User::find(1);
echo $user->append(['status_text'])->toJson();
```

设置允许输出的属性：

```php
$user = User::find(1);
echo $user->visible(['id','name','email'])->toJson();
```

模型对象可以直接被JSON序列化，例如：

```php
echo json_encode(User::find(1));
```

输出结果类似于：

```php
{"id":"1","name":"","title":"","status":"1","update_time":"1430409600","score":"90.5"}
```

如果直接`echo`一个模型对象会自动调用模型的`toJson`方法输出，例如：

```php
echo User::find(1);
```

输出的结果和上面是一样的。

