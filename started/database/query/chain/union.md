## union

UNION操作用于合并两个或多个 SELECT 语句的结果集。

使用示例：

```php
Db::field('name')
    ->table('think_user_0')
    ->union('SELECT name FROM think_user_1')
    ->union('SELECT name FROM think_user_2')
    ->select();
```

闭包用法：

```php
Db::field('name')
    ->table('think_user_0')
    ->union(function ($query) {
        $query->field('name')->table('think_user_1');
    })
    ->union(function ($query) {
        $query->field('name')->table('think_user_2');
    })
    ->select();
```

或者

```php
Db::field('name')
    ->table('think_user_0')
    ->union([
        'SELECT name FROM think_user_1',
        'SELECT name FROM think_user_2',
    ])
    ->select();
```

支持UNION ALL 操作，例如：

```php
Db::field('name')
    ->table('think_user_0')
    ->unionAll('SELECT name FROM think_user_1')
    ->unionAll('SELECT name FROM think_user_2')
    ->select();
```

或者

```php
Db::field('name')
    ->table('think_user_0')
    ->union(['SELECT name FROM think_user_1', 'SELECT name FROM think_user_2'], true)
    ->select();
```

每个union方法相当于一个独立的SELECT语句。

> UNION 内部的 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每条 SELECT 语句中的列的顺序必须相同。



