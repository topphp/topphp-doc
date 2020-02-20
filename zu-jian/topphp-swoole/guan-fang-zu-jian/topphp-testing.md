### 目录结构
```
tests
└── src
    └── DemoTest.php
```

### 命名规范
1. 测试类必须以大驼峰命名,
2. 必须以 `Test` 结尾


### 使用方法
1. 如果是普通方法测试的话可以继承 `Topphp\TopphpTesting\TestCase`
```php
class DemoTest extends TestCase
{
}
```

2. 如果是想对http接口进行测试的话可以继承 
`Topphp\TopphpTesting\HttpTestCase`

```php
class DemoTest extends HttpTestCase
{
}
```
