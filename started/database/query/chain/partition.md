## partition

`partition`方法用于`MySQL`数据库的分区查询，用法如下：

```php
// 用于查询
Db::name('log')
    ->partition(['p1','p2'])
    ->select();

// 用于写入
Db::name('user')
    ->partition('p1')
    ->insert(['name' => 'think', 'score' => 100']);
```



