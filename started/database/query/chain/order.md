## order

`order`方法用于对操作的结果排序或者优先级限制。

用法如下：

```php
Db::table('user')
->where('status', 1)
->order('id', 'desc')
->limit(5)
->select();
```

```php
 SELECT * FROM `user` WHERE `status` = 1 ORDER BY `id` desc LIMIT 5
```

> 如果没有指定`desc`或者`asc`排序规则的话，默认为`asc`。

支持使用数组对多个字段的排序，例如：

```php
Db::table('user')
->where('status', 1)
->order(['order','id'=>'desc'])
->limit(5)
->select(); 
```

最终的查询SQL可能是

```php
SELECT * FROM `user` WHERE `status` = 1 ORDER BY `order`,`id` desc LIMIT 5
```

对于更新数据或者删除数据的时候可以用于优先级限制

```php
Db::table('user')
->where('status', 1)
->order('id', 'desc')
->limit(5)
->delete(); 
```

生成的SQL

```php
DELETE FROM `user` WHERE `status` = 1 ORDER BY `id` desc LIMIT 5
```

如果你需要在`order`方法中使用mysql函数的话，必须使用下面的方式：

```php
Db::table('user')
->where('status', 1)
->orderRaw("field(name,'thinkphp','onethink','kancloud')")
->limit(5)
->select();
```



