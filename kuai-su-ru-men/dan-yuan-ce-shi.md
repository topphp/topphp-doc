### 安装
```shell
composer require topphp/topphp-testing=dev-master
```

### 使用
1.继承`Topphp\TopphpTesting\TestCase`
2.类名`*Test`
3.方法名 `test*`
4.可使用注解形式 `@test`定义单元测试函数,这样方法名就不用`test`开头

```php
<?php
declare(strict_types=1);

namespace Topphp\Test;

use Topphp\TopphpTesting\TestCase;

class DemoTest extends TestCase
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
    /**
      * @test
      */
    public function demo()
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