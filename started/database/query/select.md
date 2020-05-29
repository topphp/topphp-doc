## 查询单个数据

查询单个数据使用`find`方法：

```php
// table方法必须指定完整的数据表名
Db::table('think_user')->where('id', 1)->find();
```

最终生成的SQL语句可能是：

```php
SELECT * FROM `think_user` WHERE  `id` = 1 LIMIT 1
```

> `find`方法查询结果不存在，返回`null`，否则返回结果数组

如果希望查询数据不存在的时候返回空数组，可以使用

```php
// table方法必须指定完整的数据表名
Db::table('think_user')->where('id', 1)->findOrEmpty();
```

如果希望在没有找到数据后抛出异常可以使用

```php
Db::table('think_user')->where('id', 1)->findOrFail();
```

如果没有查找到数据，则会抛出一个`think\db\exception\DataNotFoundException`异常。

## 查询数据集

查询多个数据（数据集）使用`select`方法：

```php
Db::table('think_user')->where('status', 1)->select();
```

最终生成的SQL语句可能是：

```php
SELECT * FROM `think_user` WHERE `status` = 1
```

> `select`方法查询结果是一个数据集对象，如果需要转换为数组可以使用

```php
Db::table('think_user')->where('status', 1)->select()->toArray();
```

如果希望在没有查找到数据后抛出异常可以使用

```php
Db::table('think_user')->where('status',1)->selectOrFail();
```

如果没有查找到数据，同样也会抛出一个`think\db\exception\DataNotFoundException`异常。

如果设置了数据表前缀参数的话，可以使用

```php
Db::name('user')->where('id', 1)->find();
Db::name('user')->where('status', 1)->select();
```

> 如果你的数据表没有设置表前缀的话，那么`name`和`table`方法效果一致。

在`find`和`select`方法之前可以使用所有的链式操作（参考链式操作章节）方法。

## 值和列查询

查询某个字段的值可以用

```php
// 返回某个字段的值
Db::table('think_user')->where('id', 1)->value('name');
```

> value 方法查询结果不存在，返回 null

查询某一列的值可以用

```php
// 返回数组
Db::table('think_user')->where('status',1)->column('name');
// 指定id字段的值作为索引
Db::table('think_user')->where('status',1)->column('name', 'id');
```

如果要返回完整数据，并且添加一个索引值的话，可以使用

```php
// 指定id字段的值作为索引 返回所有数据
Db::table('think_user')->where('status',1)->column('*','id');
```

> `column`方法查询结果不存在，返回空数组

## 数据分批处理

如果你需要处理成千上百条数据库记录，可以考虑使用`chunk`方法，该方法一次获取结果集的一小块，然后填充每一小块数据到要处理的闭包，该方法在编写处理大量数据库记录的时候非常有用。

比如，我们可以全部用户表数据进行分批处理，每次处理 100 个用户记录：

```php
Db::table('think_user')->chunk(100, function($users) {
    foreach ($users as $user) {
        //
    }
});
```

你可以通过从闭包函数中返回`false`来中止对后续数据集的处理：

```php
Db::table('think_user')->chunk(100, function($users) {
    foreach ($users as $user) {
        // 处理结果集...
		if($user->status==0){
            return false;
        }
    }
});
```

也支持在`chunk`方法之前调用其它的查询方法，例如：

```php
Db::table('think_user')
->where('score','>',80)
->chunk(100, function($users) {
    foreach ($users as $user) {
        //
    }
});
```

`chunk`方法的处理默认是根据主键查询，支持指定字段，例如：

```php
Db::table('think_user')->chunk(100, function($users) {
    // 处理结果集...
    return false;
},'create_time');
```

并且支持指定处理数据的顺序。

```php
Db::table('think_user')->chunk(100, function($users) {
    // 处理结果集...
    return false;
},'create_time', 'desc');
```

> `chunk`方法一般用于命令行操作批处理数据库的数据，不适合WEB访问处理大量数据，很容易导致超时。

## 游标查询

如果你需要处理大量的数据，可以使用新版提供的游标查询功能，该查询方式利用了PHP的生成器特性，可以大幅减少大量数据查询的内存开销问题。

```php
$cursor = Db::table('user')->where('status', 1)->cursor();
foreach($cursor as $user){
	echo $user['name'];
}
```

`cursor`方法返回的是一个生成器对象，`user`变量是数据表的一条数据（数组）。

