## 查询与链式操作

使用了`TopPHP`的`BaseModel`类，查询的时候推荐使用`queryChain`方法进行查询构造，通常我们使用`ThinkPHP`构造查询时，都是像如下的方式调用：

```php
$where = [
    'name' => 'thinkphp'
];
$user = new UserDao;
$user->where($where)->select();
```

如果使用`TopPHP`提供的`BaseModel`类，链式操作就需要以`queryChain`方法开头：

```php
$where = [
    'name' => 'thinkphp'
];
$user = new UserDao;
$user->queryChain($where)->select();
```

这样是为了兼容`BaseModel`类的自动软删除功能，默认使用`queryChain`构造的查询，不使用`ThinkPHP`的软删除`use SoftDelete;`也会自动过滤软删除数据。