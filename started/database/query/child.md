## 子查询

首先构造子查询SQL，可以使用下面三种的方式来构建子查询。

## 使用`fetchSql`方法

fetchSql方法表示不进行查询而只是返回构建的SQL语句，并且不仅仅支持`select`，而是支持所有的CURD查询。

```php
$subQuery = Db::table('think_user')
    ->field('id,name')
    ->where('id', '>', 10)
    ->fetchSql(true)
    ->select();
```

生成的subQuery结果为：

```php
SELECT `id`,`name` FROM `think_user` WHERE `id` > 10 
```

## 使用`buildSql`构造子查询

```php
$subQuery = Db::table('think_user')
    ->field('id,name')
    ->where('id', '>', 10)
    ->buildSql();
```

生成的subQuery结果为：

```php
( SELECT `id`,`name` FROM `think_user` WHERE `id` > 10 )
```

调用`buildSql`方法后不会进行实际的查询操作，而只是生成该次查询的SQL语句（为了避免混淆，会在SQL两边加上括号），然后我们直接在后续的查询中直接调用。

然后使用子查询构造新的查询：

```php
Db::table($subQuery . ' a')
    ->where('a.name', 'like', 'thinkphp')
    ->order('id', 'desc')
    ->select();
```

生成的SQL语句为：

```php
SELECT * FROM ( SELECT `id`,`name` FROM `think_user` WHERE `id` > 10 ) a WHERE a.name LIKE 'thinkphp' ORDER BY `id` desc
```

## 使用闭包构造子查询

`IN/NOT IN`和`EXISTS/NOT EXISTS`之类的查询可以直接使用闭包作为子查询，例如：

```php
Db::table('think_user')
    ->where('id', 'IN', function ($query) {
        $query->table('think_profile')->where('status', 1)->field('id');
    })
    ->select();
```

生成的SQL语句是

```php
SELECT * FROM `think_user` WHERE `id` IN ( SELECT `id` FROM `think_profile` WHERE `status` = 1 )
```

```php
Db::table('think_user')
    ->whereExists(function ($query) {
        $query->table('think_profile')->where('status', 1);
    })->find();
```

生成的SQL语句为

```php
SELECT * FROM `think_user` WHERE EXISTS ( SELECT * FROM `think_profile` WHERE `status` = 1 ) 
```

> 除了上述查询条件外，比较运算也支持使用闭包子查询



