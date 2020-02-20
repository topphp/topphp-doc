### 目录结构
```
tests
└── src
    └── DemoTest.php
```

### 命名规范
1. 测试类必须以`大驼峰`命名,
2. 必须以 `Test` 结尾
3. 方法名必须以`小驼峰`命名
4. 方法名必须以 `test` 开头

### 使用方法
1. 如果是普通方法测试的话可以继承 `Topphp\TopphpTesting\TestCase`
```php
class DemoTest extends TestCase
{
    public function testIndex() {
    }
}
```

2. 如果是想对http接口进行测试的话可以继承 `Topphp\TopphpTesting\HttpTestCase`

```php
class DemoTest extends HttpTestCase
{
    public function testIndex() {
    }
}
```
