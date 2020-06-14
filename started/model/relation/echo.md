## 关联输出

关联数据的输出也可以使用`hidden`、`visible`和`append`方法进行控制，下面举例说明。

## 隐藏关联属性

如果要隐藏关联模型的属性，可以使用

```php
$list = User::with('profile')->select();
$list->hidden(['profile.email'])->toArray();
```

输出的结果中就不会包含`Profile`模型的`email`属性，如果需要隐藏多个属性可以使用

```php
$list = User::with('profile')->select();
$list->hidden(['profile' => ['address','phone','email']])->toArray();
```

## 显示关联属性

同样，可以使用visible方法来显示关联属性：

```php
$list = User::with('profile')->select();
$list->visible(['profile' => ['address','phone','email']])->toArray();
```

## 追加关联属性

追加一个`Profile`模型的额外属性（非实际数据，可能是定义了获取器方法）

```php
$list = User::with('profile')->select();
$list->append(['profile.status'])->toArray();
```

也可以追加一个额外关联对象的属性

```php
$list = User::with('profile')->select();
$list->append(['Book.name'])->toArray();
```



