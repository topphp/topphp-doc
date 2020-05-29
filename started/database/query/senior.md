## 快捷查询

快捷查询方式是**一种多字段相同查询条件**的简化写法，可以进一步简化查询条件的写法，在多个字段之间用`|`分割表示`OR`查询，用`&`分割表示`AND`查询，可以实现下面的查询，例如：

```php
Db::table('think_user')
    ->where('name|title','like','thinkphp%')
    ->where('create_time&update_time','>',0)
    ->find();
```

生成的查询SQL是：

```php
SELECT * FROM `think_user` WHERE ( `name` LIKE 'thinkphp%' OR `title` LIKE 'thinkphp%' ) AND ( `create_time` > 0 AND `update_time` > 0 ) LIMIT 1
```

> 快捷查询支持所有的查询表达式。

## 批量（字段）查询

可以进行多个条件的批量条件查询定义，例如：

```php
Db::table('think_user')
    ->where([
        ['name', 'like', 'thinkphp%'],
        ['title', 'like', '%thinkphp'],
        ['id', '>', 0],
        ['status', '=', 1],
    ])
    ->select();
```

生成的SQL语句为：

```php
SELECT * FROM `think_user` WHERE `name` LIKE 'thinkphp%' AND `title` LIKE '%thinkphp' AND `id` > 0 AND `status` = '1'
```

数组方式如果使用`exp`查询的话，一定要用`raw`方法。

```php
Db::table('think_user')
    ->where([
        ['name', 'like', 'thinkphp%'],
        ['title', 'like', '%thinkphp'],
        ['id', 'exp', Db::raw('>score')],
        ['status', '=', 1],
    ])
    ->select();
```

数组查询方式，确保你的查询数组不能被用户提交数据控制，用户提交的表单数据应该是作为查询数组的一个元素传入，如下：

```php
Db::table('think_user')
    ->where([
        ['name', 'like', $name . '%'],
        ['title', 'like', '%' . $title],
        ['id', '>', $id],
        ['status', '=', $status],
    ])
    ->select();
```

> 注意，相同的字段的多次查询条件可能会合并，如果希望某一个`where`方法里面的条件单独处理，可以使用下面的方式，避免被其它条件影响。

```php
$map = [
        ['name', 'like', 'thinkphp%'],
        ['title', 'like', '%thinkphp'],
        ['id', '>', 0],
    ];
Db::table('think_user')
    ->where([ $map ])
    ->where('status',1)
    ->select();
```

生成的SQL语句为：

```php
SELECT * FROM `think_user` WHERE ( `name` LIKE 'thinkphp%' AND `title` LIKE '%thinkphp' AND `id` > 0 ) AND `status` = '1'
```

如果使用下面的多个条件组合

```php
$map1 = [
        ['name', 'like', 'thinkphp%'],
        ['title', 'like', '%thinkphp'],
    ];
    
$map2 = [
        ['name', 'like', 'kancloud%'],
        ['title', 'like', '%kancloud'],
    ];    
    
Db::table('think_user')
    ->whereOr([ $map1, $map2 ])
    ->select();
```

生成的SQL语句为：

```php
SELECT * FROM `think_user` WHERE ( `name` LIKE 'thinkphp%' AND `title` LIKE '%thinkphp' ) OR ( `name` LIKE 'kancloud%' AND `title` LIKE '%kancloud' )
```

> 善用多维数组查询，可以很方便的拼装出各种复杂的SQL语句

## 闭包查询

```
$name = 'thinkphp';
$id = 10;
Db::table('think_user')->where(function ($query) use($name, $id) {
    $query->where('name', $name)
        ->whereOr('id', '>', $id);
})->select();
```

生成的SQL语句为：

```php
SELECT * FROM `think_user` WHERE ( `name` = 'thinkphp' OR `id` > 10 )
```

> 可见每个闭包条件两边也会自动加上括号。

## 混合查询

可以结合前面提到的所有方式进行混合查询，例如：

```php
Db::table('think_user')
    ->where('name', 'like', 'thinkphp%')
    ->where(function ($query) {
        $query->where('id', '<', 10);
    })
    ->select();
```

生成的SQL语句是：

```php
SELECT * FROM `think_user` WHERE  `name` LIKE 'thinkphp%' AND ( `id` < 10 )
```

## 字符串条件查询

对于一些实在复杂的查询，也可以直接使用原生SQL语句进行查询，例如：

```php
Db::table('think_user')
    ->whereRaw('id > 0 AND name LIKE "thinkphp%"')
    ->select();
```

为了安全起见，我们可以对字符串查询条件使用参数绑定，例如：

```php
Db::table('think_user')
    ->whereRaw('id > :id AND name LIKE :name ', ['id' => 0, 'name' => 'thinkphp%'])
    ->select();
```

## 快捷方法

系统封装了一系列快捷方法，用于简化查询，包括：

| 方法 | 作用 |
| :--- | :--- |
| `whereOr` | 字段OR查询 |
| `whereXor` | 字段XOR查询 |
| `whereNull` | 查询字段是否为Null |
| `whereNotNull` | 查询字段是否不为Null |
| `whereIn` | 字段IN查询 |
| `whereNotIn` | 字段NOT IN查询 |
| `whereBetween` | 字段BETWEEN查询 |
| `whereNotBetween` | 字段NOT BETWEEN查询 |
| `whereLike` | 字段LIKE查询 |
| `whereNotLike` | 字段NOT LIKE查询 |
| `whereExists` | EXISTS条件查询 |
| `whereNotExists` | NOT EXISTS条件查询 |
| `whereExp` | 表达式查询 |
| `whereColumn` | 比较两个字段 |

下面举例说明下两个字段比较的查询条件`whereColumn`方法的用法。

查询`update_time`大于`create_time`的用户数据

```php
Db::table('think_user')
    ->whereColumn('update_time','>','create_time')
    ->select();
```

生成的SQL语句是：

```php
SELECT * FROM `think_user` WHERE ( `update_time` > `create_time` ) 
```

查询`name`和`nickname`相同的用户数据

```php
Db::table('think_user')
    ->whereColumn('name','=','nickname')
    ->select();
```

生成的SQL语句是：

```php
SELECT * FROM `think_user` WHERE ( `name` = `nickname` )
```

相同字段条件也可以简化为

```php
Db::table('think_user')
    ->whereColumn('name','nickname')
    ->select();
```

支持数组方式比较多个字段

```php
Db::name('user')->whereColumn([
	['title', '=', 'name'],
    ['update_time', '>=', 'create_time'],
])->select();
```

生成的SQL语句是：

```php
SELECT * FROM `think_user` WHERE ( `name` = `nickname` AND `update_time` > `create_time` ) 
```

## 动态查询

查询构造器还提供了动态查询机制，用于简化查询条件，包括：

| 动态查询 | 描述 |
| :--- | :--- |
| `whereFieldName` | 查询某个字段的值 |
| `whereOrFieldName` | 查询某个字段的值 |
| `getByFieldName` | 根据某个字段查询 |
| `getFieldByFieldName` | 根据某个字段获取某个值 |

其中`FieldName`表示数据表的实际字段名称的驼峰法表示，假设数据表`user`中有`email`和`nick_name`字段，我们可以这样来查询。

```php
// 根据邮箱（email）查询用户信息
$user = Db::table('user')
	->whereEmail('thinkphp@qq.com')
    ->find();

// 根据昵称（nick_name）查询用户
$email = Db::table('user')
    ->whereNickName('like', '%流年%')
    ->select();
    
// 根据邮箱查询用户信息
$user = Db::table('user')
    ->getByEmail('thinkphp@qq.com');
    
// 根据昵称（nick_name）查询用户信息
$user = Db::table('user')
    ->field('id,name,nick_name,email')
    ->getByNickName('流年');
    
// 根据邮箱查询用户的昵称
$nickname = Db::table('user')
    ->getFieldByEmail('thinkphp@qq.com', 'nick_name');
    
// 根据昵称（nick_name）查询用户邮箱
$email = Db::table('user')
    ->getFieldByNickName('流年', 'email');
```

> `getBy`和`getFieldBy`方法只会查询一条记录，可以和其它的链式方法搭配使用

## 条件查询

查询构造器支持条件查询，例如：

```php
Db::name('user')->when($condition, function ($query) {
    // 满足条件后执行
    $query->where('score', '>', 80)->limit(10);
})->select();
```

并且支持不满足条件的分支查询

```php
Db::name('user')->when($condition, function ($query) {
    // 满足条件后执行
    $query->where('score', '>', 80)->limit(10);
}, function ($query) {
    // 不满足条件执行
    $query->where('score', '>', 60);
});
```



