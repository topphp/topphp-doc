## 查询表达式

查询表达式支持大部分的SQL查询语法，也是`ThinkPHP`查询语言的精髓，查询表达式的使用格式：

```php
where('字段名','查询表达式','查询条件');
```

除了`where`方法外，还可以支持`whereOr`，用法是一样的。为了更加方便查询，大多数的查询表达式都提供了快捷查询方法。

表达式不分大小写，支持的查询表达式有下面几种：

| 表达式 | 含义 | 快捷查询方法 |
| :--- | :--- | :--- |
| = | 等于 |  |
| &lt;&gt; | 不等于 |  |
| &gt; | 大于 |  |
| &gt;= | 大于等于 |  |
| &lt; | 小于 |  |
| &lt;= | 小于等于 |  |
| \[NOT\] LIKE | 模糊查询 | `whereLike/whereNotLike` |
| \[NOT\] BETWEEN | （不在）区间查询 | `whereBetween/whereNotBetween` |
| \[NOT\] IN | （不在）IN 查询 | `whereIn/whereNotIn` |
| \[NOT\] NULL | 查询字段是否（不）是NULL | `whereNull/whereNotNull` |
| \[NOT\] EXISTS | EXISTS查询 | `whereExists/whereNotExists` |
| \[NOT\] REGEXP | 正则（不）匹配查询（仅支持Mysql） |  |
| \[NOT\] BETWEEN TIME | 时间区间比较 | whereBetweenTime |
| &gt; TIME | 大于某个时间 | `whereTime` |
| &lt; TIME | 小于某个时间 | `whereTime` |
| &gt;= TIME | 大于等于某个时间 | `whereTime` |
| &lt;= TIME | 小于等于某个时间 | `whereTime` |
| EXP | 表达式查询，支持SQL语法 | `whereExp` |
| find in set | FIND\_IN\_SET查询 | `whereFindInSet` |

表达式查询的用法示例如下：

## 等于（=）

例如：

```php
Db::name('user')->where('id','=',100)->select();
```

和下面的查询等效

```php
Db::name('user')->where('id',100)->select();
```

最终生成的SQL语句是：

```php
SELECT * FROM `think_user` WHERE  `id` = 100
```

## 不等于（&lt;&gt;）

例如：

```php
Db::name('user')->where('id','<>',100)->select();
```

最终生成的SQL语句是：

```php
SELECT * FROM `think_user` WHERE  `id` <> 100
```

## 大于（&gt;）

例如：

```php
Db::name('user')->where('id','>',100)->select();
```

最终生成的SQL语句是：

```php
SELECT * FROM `think_user` WHERE  `id` > 100
```

## 大于等于（&gt;=）

例如：

```php
Db::name('user')->where('id','>=',100)->select();
```

最终生成的SQL语句是：

```php
SELECT * FROM `think_user` WHERE  `id` >= 100
```

## 小于（&lt;）

例如：

```php
Db::name('user')->where('id', '<', 100)->select();
```

最终生成的SQL语句是：

```php
SELECT * FROM `think_user` WHERE  `id` < 100
```

## 小于等于（&lt;=）

例如：

```php
Db::name('user')->where('id', '<=', 100)->select();
```

最终生成的SQL语句是：

```php
SELECT * FROM `think_user` WHERE  `id` <= 100
```

## \[NOT\] LIKE： 同sql的LIKE

例如：

```php
Db::name('user')->where('name', 'like', 'thinkphp%')->select();
```

最终生成的SQL语句是：

```php
SELECT * FROM `think_user` WHERE  `name` LIKE 'thinkphp%'
```

`like`查询支持使用数组

```php
Db::name('user')->where('name', 'like', ['%think','php%'],'OR')->select();
```

实际生成的SQL语句为：

```php
SELECT * FROM `think_user` WHERE  (`name` LIKE '%think' OR `name` LIKE 'php%')
```

为了更加方便，应该直接使用`whereLike`方法

```php
Db::name('user')->whereLike('name','thinkphp%')->select();
Db::name('user')->whereNotLike('name','thinkphp%')->select();
```

## \[NOT\] BETWEEN ：同sql的\[not\] between

查询条件支持字符串或者数组，例如：

```php
Db::name('user')->where('id','between','1,8')->select();
```

和下面的等效：

```php
Db::name('user')->where('id','between',[1,8])->select();
```

最终生成的SQL语句都是：

```php
SELECT * FROM `think_user` WHERE  `id` BETWEEN 1 AND 8
```

或者使用快捷查询方法：

```php
Db::name('user')->whereBetween('id','1,8')->select();
Db::name('user')->whereNotBetween('id','1,8')->select();
```

## \[NOT\] IN： 同sql的\[not\] in

查询条件支持字符串或者数组，例如：

```php
Db::name('user')->where('id','in','1,5,8')->select();
```

和下面的等效：

```php
Db::name('user')->where('id','in',[1,5,8])->select();
```

最终的SQL语句为：

```php
SELECT * FROM `think_user` WHERE  `id` IN (1,5,8)
```

或者使用快捷查询方法：

```php
Db::name('user')->whereIn('id','1,5,8')->select();
Db::name('user')->whereNotIn('id','1,5,8')->select();
```

> `[NOT] IN`查询支持使用闭包方式

## \[NOT\] NULL ：

查询字段是否（不）是`Null`，例如：

```php
Db::name('user')->where('name', null)
->where('email','null')
->where('name','not null')
->select();
```

实际生成的SQL语句为：

```php
SELECT * FROM `think_user` WHERE  `name` IS NULL  AND `email` IS NULL  AND `name` IS NOT NULL

```

如果你需要查询一个字段的值为字符串`null`或者`not null`，应该使用：

```php
Db::name('user')->where('title','=', 'null')
->where('name','=', 'not null')
->select();
```

推荐的方式是使用`whereNull`和`whereNotNull`方法查询。

```php
Db::name('user')->whereNull('name')
->whereNull('email')
->whereNotNull('name')
->select();
```

## EXP：表达式

支持更复杂的查询情况 例如：

```php
Db::name('user')->where('id','in','1,3,8')->select();
```

可以改成：

```php
Db::name('user')->where('id','exp',' IN (1,3,8) ')->select();
```

`exp`查询的条件不会被当成字符串，所以后面的查询条件可以使用任何SQL支持的语法，包括使用函数和字段名称。

推荐使用`whereExp`方法查询

```php
Db::name('user')->whereExp('id', 'IN (1,3,8) ')->select();
```



