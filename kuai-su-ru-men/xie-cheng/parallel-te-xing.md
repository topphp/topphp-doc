### Parallel 特性
`Parallel` 特性是 `topphp` 基于 `WaitGroup` 特性抽象出来的一个更便捷的使用方法，我们通过一段代码来演示一下。
```php
<?php
/**
 * 凯拓软件 [临渊羡鱼不如退而结网,凯拓与你一同成长]
 * Project: topphp-swoole
 * Date: 2020/2/16 23:49
 * Author: sleep <sleep@kaituocn.com>
 */

namespace Topphp\Test\coroutine;

use Swoole\Coroutine\Scheduler;
use Swoole\Coroutine;
use Topphp\TopphpSwoole\coroutine\Parallel;
use Topphp\TopphpSwoole\exception\ParallelExecutionException;
use Topphp\TopphpTesting\TestCase;

class ParallelTest extends TestCase
{
    public function testAddParallel()
    {
        $parallel = new Parallel();
        $parallel->add(function () {
            sleep(2);
            return Coroutine::getCid();
        });
        $parallel->add(function () {
            sleep(2);
            return Coroutine::getCid();
        });
        try {
            // $results 结果为 [1, 2]
            $results = $parallel->wait();
            var_dump($results);
        } catch (ParallelExecutionException $e) {
            // 获取协程中的返回值。
            var_dump($e->getResults());
            // 获取协程中出现的异常。
            var_dump($e->getThrowables());
        }
        $this->assertEquals($results, [2, 3]);
    }
}

```
> 通过上面的代码我们可以看到仅花了 2 秒就得到了两个不同的协程的 ID，在调用 `add(callable $callable)` 的时候 `Parallel` 类会为之自动创建一个协程，并加入到 `WaitGroup` 的调度去。

我们还可以通过 parallel(array $callables) 函数进行更进一步的简化上面的代码，达到同样的目的，下面为简化后的代码:
```php
    public function testEasy()
    {
        $result = parallels([
            function () {
                sleep(3);
                return Coroutine::getCid();
            },
            function () {
                sleep(3);
                return Coroutine::getCid();
            }
        ]);
        var_dump($result);
        $this->assertIsArray($result);
    }
```

### 限制 Parallel 最大同时运行的协程数
当我们添加到 Parallel 里的任务有很多时，假设都是一些请求任务，那么一瞬间发出全部请求很有可能会导致对端服务因为一瞬间接收到了大量的请求而处理不过来，有宕机的风险，所以需要对对端进行适当的保护，但我们又希望可以通过 Parallel 机制来加速这些请求的耗时，那么可以通过在实例化 Parallel 对象时传递第一个参数，来设置最大运行的协程数，比如我们希望最大设置的协程数为 5 ，也就意味着 Parallel 里最多只会有 5 个协程在运行，只有当 5 个里有协程完成结束后，后续的协程才会继续启动，直至所有协程完成任务，示例代码如下：

```php
$parallel = new Parallel(5);
for ($i = 0; $i < 20; $i++) {
    $parallel->add(function () {
        sleep(1);
        return Coroutine::id();
    });
} 

try{
   $results = $parallel->wait(); 
} catch(ParallelExecutionException $e){
    // $e->getResults() 获取协程中的返回值。
    // $e->getThrowables() 获取协程中出现的异常。
}
```

