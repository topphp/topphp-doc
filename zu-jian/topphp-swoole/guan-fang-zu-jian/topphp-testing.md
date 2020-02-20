### 介绍

> 该组件是基于 `phpunit` 进行的二次封装, 命令行工具为 `co-phpunit`, 该工具为协程版的单元测试命令行。运行swoole代码时，要使用该命令进行单元测试。

> 可以通过composer脚本进行测试:

1. 打开 `composer.json` 文件,找到
```json
{
    "scripts": {
        "test": "./vendor/bin/co-phpunit -c ./phpunit.xml.dist"
    }
}
```
2. 运行脚本可进行全部方法测试和指定方法测试
  ```shell 
# 运行全部单元测试方法
composer test
# 指定运行单元测试方法
composer test -- --filter=testIndex
```

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
