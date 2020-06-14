## 更新数据

可以使用save方法更新数据

```php
Db::name('user')
    ->save(['id' => 1, 'name' => 'thinkphp']);
```

或者使用`update`方法。

```php
Db::name('user')
    ->where('id', 1)
    ->update(['name' => 'thinkphp']);
```

实际生成的SQL语句可能是：

```php
UPDATE `think_user`  SET `name`='thinkphp'  WHERE  `id` = 1
```

> `update`方法返回影响数据的条数，没修改任何数据返回 0

支持使用`data`方法传入要更新的数据

```php
Db::name('user')
    ->where('id', 1)
    ->data(['name' => 'thinkphp'])
    ->update();
```

> 如果`update`方法和`data`方法同时传入更新数据，则以`update`方法为准。

如果数据中包含主键，可以直接使用：

```php
Db::name('user')
    ->update(['name' => 'thinkphp','id' => 1]);
```

实际生成的SQL语句和前面用法是一样的：

```php
UPDATE `think_user`  SET `name`='thinkphp'  WHERE  `id` = 1
```

如果要更新的数据需要使用`SQL`函数或者其它字段，可以使用下面的方式：

```php
Db::name('user')
    ->where('id',1)
    ->exp('name','UPPER(name)')
    ->update();
```

实际生成的SQL语句：

```php
UPDATE `think_user`  SET `name` = UPPER(name)  WHERE  `id` = 1
```

支持使用`raw`方法进行数据更新。

```php
Db::name('user')
    ->where('id', 1)
    ->update([
        'name'		=>	Db::raw('UPPER(name)'),
        'score'		=>	Db::raw('score-3'),
        'read_time'	=>	Db::raw('read_time+1')
    ]);
```

## 自增/自减

可以使用`inc/dec`方法自增或自减一个字段的值（ 如不加第二个参数，默认步长为1）。

```php
// score 字段加 1
Db::table('think_user')
    ->where('id', 1)
    ->inc('score')
    ->update();

// score 字段加 5
Db::table('think_user')
    ->where('id', 1)
    ->inc('score', 5)
    ->update();

// score 字段减 1
Db::table('think_user')
    ->where('id', 1)
    ->dec('score')
    ->update();

// score 字段减 5
Db::table('think_user')
    ->where('id', 1)
    ->dec('score', 5)
    ->update();
```

最终生成的SQL语句可能是：

```php
UPDATE `think_user`  SET `score` = `score` + 1  WHERE  `id` = 1 
UPDATE `think_user`  SET `score` = `score` + 5  WHERE  `id` = 1
UPDATE `think_user`  SET `score` = `score` - 1  WHERE  `id` = 1
UPDATE `think_user`  SET `score` = `score` - 5  WHERE  `id` = 1
```



