## 添加一条数据

可以使用`save`方法统一写入数据，自动判断是新增还是更新数据（以写入数据中是否存在主键数据为依据）。

```php
$data = ['foo' => 'bar', 'bar' => 'foo'];
Db::name('user')->save($data);
```

或者使用`insert`方法向数据库提交数据

```php
$data = ['foo' => 'bar', 'bar' => 'foo'];
Db::name('user')->insert($data);
```

> `insert`方法添加数据成功返回添加成功的条数，通常情况返回 1

> 如果你的数据表里面没有`foo`或者`bar`字段，那么就会抛出异常。

如果不希望抛出异常，可以使用下面的方法：

```php
$data = ['foo' => 'bar', 'bar' => 'foo'];
Db::name('user')->strict(false)->insert($data);
```

不存在字段的值将会直接抛弃。

如果是mysql数据库，支持`replace`写入，例如：

```php
$data = ['foo' => 'bar', 'bar' => 'foo'];
Db::name('user')->replace()->insert($data);
```

添加数据后如果需要返回新增数据的自增主键，可以使用`insertGetId`方法新增数据并返回主键值：

```php
$userId = Db::name('user')->insertGetId($data);
```

> `insertGetId`方法添加数据成功返回添加数据的自增主键

## 添加多条数据

添加多条数据直接向 Db 类的`insertAll`方法传入需要添加的数据（通常是二维数组）即可。

```php
$data = [
    ['foo' => 'bar', 'bar' => 'foo'],
    ['foo' => 'bar1', 'bar' => 'foo1'],
    ['foo' => 'bar2', 'bar' => 'foo2']
];
Db::name('user')->insertAll($data);
```

> `insertAll`方法添加数据成功返回添加成功的条数

如果是mysql数据库，支持`replace`写入，例如：

```php
$data = [
    ['foo' => 'bar', 'bar' => 'foo'],
    ['foo' => 'bar1', 'bar' => 'foo1'],
    ['foo' => 'bar2', 'bar' => 'foo2']
];
Db::name('user')->replace()->insertAll($data);
```

> 确保要批量添加的数据字段是一致的

如果批量插入的数据比较多，可以指定分批插入，使用`limit`方法指定每次插入的数量限制。

```php
$data = [
    ['foo' => 'bar', 'bar' => 'foo'],
    ['foo' => 'bar1', 'bar' => 'foo1'],
    ['foo' => 'bar2', 'bar' => 'foo2']
    ...
];
// 分批写入 每次最多100条数据
Db::name('user')
    ->limit(100)
    ->insertAll($data);
```



