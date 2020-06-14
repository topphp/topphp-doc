## 聚合查询

在应用中我们经常会用到一些统计数据，例如当前所有（或者满足某些条件）的用户数、所有用户的最大积分、用户的平均成绩等等，ThinkPHP为这些统计操作提供了一系列的内置方法，包括：

| 方法 | 说明 |
| :--- | :--- |
| count | 统计数量，参数是要统计的字段名（可选） |
| max | 获取最大值，参数是要统计的字段名（必须） |
| min | 获取最小值，参数是要统计的字段名（必须） |
| avg | 获取平均值，参数是要统计的字段名（必须） |
| sum | 获取总分，参数是要统计的字段名（必须） |

> 聚合方法如果没有数据，默认都是0，聚合查询都可以配合其它查询条件

## 用法示例

获取用户数：

```php
Db::table('think_user')->count();
```

实际生成的SQL语句是：

```php
SELECT COUNT(*) AS tp_count FROM `think_user` LIMIT 1
```

或者根据字段统计：

```php
Db::table('think_user')->count('id');
```

生成的SQL语句是：

```php
SELECT COUNT(id) AS tp_count FROM `think_user` LIMIT 1
```

获取用户的最大积分：

```php
Db::table('think_user')->max('score');
```

生成的SQL语句是：

```php
SELECT MAX(score) AS tp_max FROM `think_user` LIMIT 1
```

如果你要获取的最大值不是一个数值，可以使用第二个参数关闭强制转换

```php
Db::table('think_user')->max('name',false);
```

获取积分大于0的用户的最小积分：

```php
Db::table('think_user')->where('score', '>', 0)->min('score');
```

和max方法一样，min也支持第二个参数用法

```php
Db::table('think_user')->where('score', '>', 0)->min('name',false);
```

获取用户的平均积分：

```php
Db::table('think_user')->avg('score');
```

生成的SQL语句是：

```php
SELECT AVG(score) AS tp_avg FROM `think_user` LIMIT 1
```

统计用户的总成绩：

```php
Db::table('think_user')->where('id',10)->sum('score');
```

生成的SQL语句是：

```php
SELECT SUM(score) AS tp_sum FROM `think_user` LIMIT 1
```

如果你要使用`group`进行聚合查询，需要自己实现查询，例如：

```php
Db::table('score')->field('user_id,SUM(score) AS sum_score')->group('user_id')->select();
```



