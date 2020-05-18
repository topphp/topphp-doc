### WaitGroup 特性

在Swoole4中可以使用channel实现协程间的通信、依赖管理、协程同步。基于channel可以很容易地实现Golang的sync.WaitGrup功能。

`WaitGroup`是基于`Channel`衍生出来的一个特性，在`topphp`里，`WaitGroup`的用途是使得主协程一直阻塞等待直到所有相关的子协程都已经完成了任务后再继续运行，这里说到的阻塞等待是仅对于主协程（即当前协程）来说的，并不会阻塞当前进程。

> 注意 `WaitGroup` 本身也需要在协程内才能使用


### 示例代码
1.add方法增加计数
2.done表示任务已完成
3.wait等待所有任务完成恢复当前协程的执行
4.WaitGroup对象可以复用，add、done、wait之后可以再次使用
```php
<?php
/**
 * 凯拓软件 [临渊羡鱼不如退而结网,凯拓与你一同成长]
 * Project: topphp-swoole
 * Date: 2020/2/16 23:17
 * Author: sleep <sleep@kaituocn.com>
 */

namespace Topphp\Test\coroutine;

use PHPUnit\Framework\TestCase;
use Swoole\Coroutine;
use Swoole\Coroutine\Http\Client;
use Topphp\TopphpSwoole\coroutine\WaitGroup;

class WaitGroupTest extends TestCase
{
    public function testDone()
    {
        $wg     = new WaitGroup();
        $result = [];
        // 添加2个协程队列
        $wg->add(2);
        //启动第一个协程
        Coroutine::create(function () use ($wg, &$result) {
            //启动一个协程客户端client，请求淘宝首页
            $cli = new Client('www.taobao.com', 443, true);
            $cli->setHeaders([
                'Host'            => "www.taobao.com",
                "User-Agent"      => 'Chrome/49.0.2587.3',
                'Accept'          => 'text/html,application/xhtml+xml,application/xml',
                'Accept-Encoding' => 'gzip',
            ]);
            $cli->get('/');

            $result['a'] = 'a';
            $result['taobao'] = $cli->getBody();
            $cli->close();
            $wg->done();
        });

        //启动第二个协程
        Coroutine::create(function () use ($wg, &$result) {
            //启动一个协程客户端client，请求百度首页
            $cli = new Client('www.baidu.com', 443, true);
            $cli->setHeaders([
                'Host'            => "www.baidu.com",
                "User-Agent"      => 'Chrome/49.0.2587.3',
                'Accept'          => 'text/html,application/xhtml+xml,application/xml',
                'Accept-Encoding' => 'gzip',
            ]);
            $cli->get('/');
            $result['b'] = 'b';
            $result['baidu'] = $cli->getBody();
            $cli->close();
            $wg->done();
        });

        //挂起当前协程，等待所有任务完成后恢复
        $wg->wait();
        //这里 $result 包含了 2 个任务执行结果
        var_dump($result);
        $this->assertTrue(true);
    }
}
```

  


