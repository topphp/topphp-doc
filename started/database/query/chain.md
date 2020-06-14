## 链式操作

数据库提供的链式操作方法，可以有效的提高数据存取的代码清晰度和开发效率，并且支持所有的CURD操作（原生查询不支持链式操作）。

使用也比较简单，假如我们现在要查询一个User表的满足状态为1的前10条记录，并希望按照用户的创建时间排序 ，代码如下：

```php
Db::table('think_user')
    ->where('status',1)
    ->order('create_time')
    ->limit(10)
    ->select();
```

这里的`where`、`order`和`limit`方法就被称之为链式操作方法，除了`select`方法必须放到最后一个外（因为`select`方法并不是链式操作方法），链式操作的方法调用顺序没有先后，例如，下面的代码和上面的等效：

```php
Db::table('think_user')
    ->order('create_time')
    ->limit(10)
    ->where('status',1)
    ->select();
```

其实不仅仅是查询方法可以使用连贯操作，包括所有的CURD方法都可以使用，例如：

```php
Db::table('think_user')
    ->where('id',1)
    ->field('id,name,email')
    ->find(); 

Db::table('think_user')
    ->where('status',1)
    ->where('id',1)
    ->delete();
```

每次`Db`类的静态方法调用是创建一个新的查询对象实例，如果你需要多次复用使用链式操作值，可以使用下面的方法。

```php
$user = Db::table('user');
$user->order('create_time')
    ->where('status',1)
    ->select();

// 会自动带上前面的where条件和order排序的值    
$user->where('id', '>', 0)->select();
```

当前查询对象在查询之后仍然会保留链式操作的值，除非你调用`removeOption`方法清空链式操作的值。

```php
$user = Db::table('think_user');
$user->order('create_time')
    ->where('status',1)
    ->select();

// 清空where查询条件值 保留其它链式操作   
$user->removeOption('where')
    ->where('id', '>', 0)
    ->select();
```

系统支持的链式操作方法包含：

| 连贯操作 | 作用 | 支持的参数类型 |
| :--- | :--- | :--- |
| where\* | 用于AND查询 | 字符串、数组和对象 |
| whereOr\* | 用于OR查询 | 字符串、数组和对象 |
| whereTime\* | 用于时间日期的快捷查询 | 字符串 |
| table | 用于定义要操作的数据表名称 | 字符串和数组 |
| alias | 用于给当前数据表定义别名 | 字符串 |
| field\* | 用于定义要查询的字段（支持字段排除） | 字符串和数组 |
| order\* | 用于对结果排序 | 字符串和数组 |
| limit | 用于限制查询结果数量 | 字符串和数字 |
| page | 用于查询分页（内部会转换成limit） | 字符串和数字 |
| group | 用于对查询的group支持 | 字符串 |
| having | 用于对查询的having支持 | 字符串 |
| join\* | 用于对查询的join支持 | 字符串和数组 |
| union\* | 用于对查询的union支持 | 字符串、数组和对象 |
| view\* | 用于视图查询 | 字符串、数组 |
| distinct | 用于查询的distinct支持 | 布尔值 |
| lock | 用于数据库的锁机制 | 布尔值 |
| cache | 用于查询缓存 | 支持多个参数 |
| with\* | 用于关联预载入 | 字符串、数组 |
| bind\* | 用于数据绑定操作 | 数组或多个参数 |
| comment | 用于SQL注释 | 字符串 |
| force | 用于数据集的强制索引 | 字符串 |
| master | 用于设置主服务器读取数据 | 布尔值 |
| strict | 用于设置是否严格检测字段名是否存在 | 布尔值 |
| sequence | 用于设置Pgsql的自增序列名 | 字符串 |
| failException | 用于设置没有查询到数据是否抛出异常 | 布尔值 |
| partition | 用于设置分区信息 | 数组 字符串 |
| replace | 用于设置使用REPLACE方式写入 | 布尔值 |
| extra | 用于设置额外查询规则 | 字符串 |
| duplicate | 用于设置DUPLCATE信息 | 数组 字符串 |

> 所有的连贯操作都返回当前的模型实例对象（this），其中带\*标识的表示支持多次调用。



