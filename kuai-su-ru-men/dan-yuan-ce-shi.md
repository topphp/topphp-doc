### 安装
```shell
composer require topphp/topphp-testing=dev-master
```

### 使用
继承`Topphp\TopphpTesting\TestCase`
```php
<?php
declare(strict_types=1);

namespace Topphp\Test;

use Topphp\TopphpTesting\TestCase;

class TestDemo extends TestCase
{
    public function testIndex()
    {
        $stack = new \SplStack();
        $stack->push('1');
        $stack->push('2');
        echo $stack->pop() . PHP_EOL;
        echo $stack->pop() . PHP_EOL;

        $queue = new \SplQueue();
        $queue->push(1);
        $queue->push(2);
        var_dump($queue->pop());
        $this->assertTrue(true);
    }
}
```