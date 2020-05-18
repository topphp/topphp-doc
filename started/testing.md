##TopPHP 单元测试组件

### 安装

```
composer require topphp/topphp-testing
```

### 使用

1.继承`Topphp\TopphpTesting\HttpTestCase`
2.类名`*Test`
3.方法名 `test*`
4.可使用注解形式`(@test)`定义单元测试函数,这样方法名就不用`test`开头

```php

<?php
declare(strict_types=1);

namespace Topphp\Test;

use Topphp\TopphpTesting\HttpTestCase;

class DemoTest extends HttpTestCase
{
    public function testIndex()
    {
        $res = true;
        $this->assertTrue($res !== false);
    }
}
```