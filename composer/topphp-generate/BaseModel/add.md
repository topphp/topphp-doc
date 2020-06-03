## 新增

通常我们新增一条数据只需要构造存储数据数组直接传给`add`方法即可：

```php
$data = [
    'name' => 'thinkphp',
    'email' => 'thinkphp@qq.com'
];
$user = new UserDao;
$user->add($data);
```
> `add`方法不同于`ThinkPHP`的`save`方法，它的新增返回的是包含自增主键值的当前新增数据信息，省去了开发者再次获取自增主键并赋值的步骤，默认会过滤数据库不存在的字段。

