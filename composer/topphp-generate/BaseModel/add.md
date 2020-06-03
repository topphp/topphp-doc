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