## limit

`limit`方法主要用于指定查询和操作的数量。

> `limit`方法可以兼容所有的数据库驱动类的

## 限制结果数量

例如获取满足要求的10个用户，如下调用即可：

```php
Db::table('user')
    ->where('status',1)
    ->field('id,name')
    ->limit(10)
    ->select();
```

`limit`方法也可以用于写操作，例如更新满足要求的3条数据：

```php
Db::table('user')
->where('score',100)
->limit(3)
->update(['level'=>'A']);
```

如果用于`insertAll`方法的话，则可以分批多次写入，每次最多写入`limit`方法指定的数量。

```php
Db::table('user')
->limit(100)
->insertAll($userList);
```

## 分页查询

用于文章分页查询是`limit`方法比较常用的场合，例如：

```php
Db::table('article')->limit(10,25)->select();
```

表示查询文章数据，从第10行开始的25条数据（可能还取决于where条件和order排序的影响 这个暂且不提）。

对于大数据表，尽量使用`limit`限制查询结果，否则会导致很大的内存开销和性能问题。

