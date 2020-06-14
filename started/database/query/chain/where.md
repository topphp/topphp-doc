## where

`where`方法的用法是ThinkPHP查询语言的精髓，也是ThinkPHP`ORM`的重要组成部分和亮点所在，可以完成包括普通查询、表达式查询、快捷查询、区间查询、组合查询在内的查询操作。`where`方法的参数支持的变量类型包括字符串、数组和闭包。

> 和`where`方法相同用法的方法还包括`whereOr`、`whereIn`等一系列快捷查询方法，下面仅以`where`为例说明用法。

## 表达式查询

> 表达式查询是官方推荐使用的查询方式

查询表达式的使用格式：

```php
Db::table('think_user')
    ->where('id','>',1)
    ->where('name','thinkphp')
    ->select(); 
```

更多的表达式查询语法，可以参考前面的查询表达式部分。

## 数组条件

数组方式有两种查询条件类型：关联数组和索引数组。

### 关联数组

主要用于等值`AND`条件，例如：

```php
// 传入数组作为查询条件
Db::table('think_user')->where([
	'name'	=>	'thinkphp',
    'status'=>	1
])->select(); 
```

最后生成的SQL语句是

```php
SELECT * FROM think_user WHERE `name`='thinkphp' AND status = 1
```

### 索引数组

索引数组方式批量设置查询条件，使用方式如下：

```php
// 传入数组作为查询条件
Db::table('think_user')->where([
	['name','=','thinkphp'],
    ['status','=',1]
])->select(); 
```

最后生成的SQL语句是

```php
SELECT * FROM think_user WHERE `name`='thinkphp' AND status = 1
```

如果需要事先组装数组查询条件，可以使用：

```php
$map[] = ['name','like','think'];
$map[] = ['status','=',1];
```

> 数组方式查询还有一些额外的复杂用法，我们会在后面的高级查询章节提及。

## 字符串条件

使用字符串条件直接查询和操作，例如：

```php
Db::table('think_user')->whereRaw('type=1 AND status=1')->select(); 
```

最后生成的SQL语句是

```php
SELECT * FROM think_user WHERE type=1 AND status=1
```

> 注意使用字符串查询条件和表达式查询的一个区别在于，不会对查询字段进行避免关键词冲突处理。

使用字符串条件的时候，如果需要传入变量，建议配合预处理机制，确保更加安全，例如：

```php
Db::table('think_user')
->whereRaw("id=:id and username=:name", ['id' => 1 , 'name' => 'thinkphp'])
->select();
```



