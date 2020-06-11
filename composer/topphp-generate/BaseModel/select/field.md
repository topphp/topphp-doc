## 根据字段筛选

### selectSameField 查询某个字段的值相同的数据

> selectSameField \( '主键id', '字段名', '查询排序', '\[ false \]是否返回model对象' \);

`selectSameField`方法用于查询同一张表指定字段值相同的数据，支持结果排序，例如查询`id`为`1`的这条数据`nickname`字段值相同的数据：

```php
$user = new UserDao;
$user->selectSameField(1, "nickname");
```



