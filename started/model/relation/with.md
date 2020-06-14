## 关联预载入

关联查询的预查询载入功能，主要解决了`N+1`次查询的问题，例如下面的查询如果有3个记录，会执行4次查询：

```php
$list = User::select([1,2,3]);
foreach($list as $user){
    // 获取用户关联的profile模型数据
    dump($user->profile);
}
```

如果使用关联预查询功能，就可以变成2次查询（对于一对一关联来说，如果使用`withJoin`方式只有一次查询），有效提高性能。

```php
$list = User::with(['profile'])->select([1,2,3]);
foreach($list as $user){
    // 获取用户关联的profile模型数据
    dump($user->profile);
}
```

支持预载入多个关联，例如：

```php
$list = User::with(['profile', 'book'])->select([1,2,3]);
```

> `with`方法只能调用一次，请不要多次调用，如果需要对多个关联模型预载入使用数组即可。

也可以支持嵌套预载入，例如：

```php
$list = User::with(['profile.phone'])->select([1,2,3]);
foreach($list as $user){
    // 获取用户关联的phone模型
    dump($user->profile->phone);
}
```

支持使用数组方式定义嵌套预载入，例如下面的预载入要同时获取用户的`Profile`关联模型的`Phone`、`Job`和`Img`子关联模型数据：

```php
$list = User::with(['profile'=>['phone','job','img']])->select([1,2,3]);
foreach($list as $user){
    // 获取用户关联
    dump($user->profile->phone);
    dump($user->profile->job);    
    dump($user->profile->img);    
}
```

如果要指定属性查询，可以使用：

```php
$list = User::field('id,name')->with(['profile' => function($query){
	$query->field('user_id,email,phone');
}])->select([1,2,3]);

foreach($list as $user){
    // 获取用户关联的profile模型数据
    dump($user->profile);
}
```

> 记得指定属性的时候一定要包含关联键。

对于一对多关联来说，如果需要设置返回的关联数据数量，可以使用`withLimit`方法。

```php
Article::with(['comments' => function(Relation $query) {
    $query->order('create_time', 'desc')->withLimit(3);
}])->select();
```

> 注意这里的类型约束必须使用`think\model\Relation`，因为`withLimit`方法是关联类才有的方法

关联预载入名称是关联方法名，支持传入方法名的小写和下划线定义方式，例如如果关联方法名是`userProfile`和`userBook`的话：

```php
$list = User::with(['userProfile','userBook'])->select([1,2,3]);
```

和下面的方法是等效的：

```php
$list = User::with(['user_profile','user_book'])->select([1,2,3]);
```

区别在于你获取关联数据的时候必须和传入的关联名称保持一致。

```php
$user = User::with(['userProfile'])->find(1);
dump($user->userProfile);

$user = User::with(['user_profile'])->find(1);
dump($user->user_profile);
```

一对一关联预载入支持两种方式：`JOIN`方式（一次查询）和`IN`方式（两次查询，默认方式），如果要使用`JOIN`方式关联预载入，可以使用`withJoin`方法。

```php
$list = User::withJoin(['profile' => function(Relation $query){
	$query->withField('truename,email');
}])->select([1,2,3]);
```

## 延迟预载入

有些情况下，需要根据查询出来的数据来决定是否需要使用关联预载入，当然关联查询本身就能解决这个问题，因为关联查询是惰性的，不过用预载入的理由也很明显，性能具有优势。

延迟预载入仅针对多个数据的查询，因为单个数据的查询用延迟预载入和关联惰性查询没有任何区别，所以不需要使用延迟预载入。

如果你的数据集查询返回的是数据集对象，可以使用调用数据集对象的`load`实现延迟预载入：

```php
// 查询数据集
$list = User::select([1,2,3]);
// 延迟预载入
$list->load(['cards']);
foreach($list as $user){
    // 获取用户关联的card模型数据
    dump($user->cards);
}
```

## 关联预载入缓存

关联预载入可以支持查询缓存，例如：

```php
$list = User::with(['profile'])->withCache(30)->select([1,2,3]);
```

表示对关联数据缓存30秒。

如果你有多个关联数据，也可以仅仅缓存部分关联

```php
$list = User::with(['profile', 'book'])->withCache(['profile'],30)->select([1,2,3]);
```

对于延迟预载入查询的话，可以在第二个参数传入缓存参数。

```php
// 查询数据集
$list = User::select([1,2,3]);
// 延迟预载入
$list->load(['cards'], 30);
```



