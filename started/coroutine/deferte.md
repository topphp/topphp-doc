## defer特性

### Coroutine::defer

需要Swoole版本 &gt;= 4.2.9  
`defer` 用于资源的释放, 会在协程关闭之前\(即协程函数执行完毕时\)进行调用, 就算抛出了异常, 已注册的defer也会被执行.

需要注意的是, 它的调用顺序和`go`语言中的`defer`一样是逆序的（先进后出）, 也就是先注册`defer`的后执行, 在底层`defer`列表是一个`stack`, 先进后出. 逆序符合资源释放的正确逻辑, 后申请的资源可能是基于先申请的资源的, 如先释放先申请的资源, 后申请的资源可能就难以释放。

与`Go`语言不同的是`Swoole4`的`defer`是协程退出前执行。这是因为`Go`没有提供析构方法，而`PHP`对象有析构函数，使用`__destruct`就可以实现`Go`的`defer`特性，参见[如何实现 Go defer](https://wiki.swoole.com/wiki/page/p-go_defer.html)。


### 示例
>当我们希望在协程结束时运行一些代码时，可以通过 defer(callable $callable) 函数或 Coroutine::defer(callable $callable) 将一段函数以 栈(stack) 的形式储存起来，栈(stack) 内的函数会在当前协程结束时以 先进后出 的流程逐个执行。

```php
<?php

namespace Topphp\Test\coroutine;

use Swoole\Coroutine;
use Topphp\TopphpSwoole\coroutine\Context;
use Topphp\TopphpTesting\TestCase;

class ContextTest extends TestCase
{
    public function testContext()
    {
        Coroutine::create(function () {
            Context::set('info', [1, 2, 3], Coroutine::getuid());
            $info = Context::get('info', Coroutine::getuid());  // get context of this coroutine
            var_dump($info);
            $this->assertEquals($info, [1, 2, 3], '1 succss');
            Coroutine::defer(function () {
                Context::delete('info', Coroutine::getuid());       // delete
                // get context of this coroutine
                $info = Context::get(
                    'info',
                    Coroutine::getuid()
                );
                var_dump($info);
                $this->assertNull($info, '2 success');
            });
        });
    }
}
```


