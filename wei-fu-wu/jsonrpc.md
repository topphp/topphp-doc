
>jsonrpc服务基于 `topphp-swoole` 组件,底层使用tcp服务(TcpServer::class)
使用前请先安装:
```shell
conposer require topphp/topphp-swoole
```

#### 使用
> 服务有两种角色，一种是 服务提供者，即为其它服务提供服务的服务，另一种是 服务消费者，即依赖其它服务的服务，
> 一个服务既可能是服务提供者，同时又是服务消费者。
 
##### 定义服务提供者

1. 通过 @Rpc 注解对一个类进行定义

2. 继承 `Topphp\TopphpSwoole\services\RpcProviderService` 

>即可发布这个服务了：

```php
<?php
declare (strict_types=1);

namespace app\service;

use Topphp\TopphpSwoole\annotation\Rpc;
use Topphp\TopphpSwoole\services\RpcProviderService;

/**
 * @Rpc(serviceName="cinemaService",serverName="cinema-server",protocol="jsonrpc")
 */
class CinemaService extends RpcProviderService
{
    public function test1($a, $b)
    {
        return $a - $b;
    }
}
```

##### `@Rpc` 注解的参数解释

```php
<?php

namespace Topphp\TopphpSwoole\annotation;

use Doctrine\Common\Annotations\Annotation;
use Doctrine\Common\Annotations\Annotation\Enum;
use Doctrine\Common\Annotations\Annotation\Required;
use Doctrine\Common\Annotations\Annotation\Target;

/**
 * @Annotation
 * @Target("CLASS")
 */
final class Rpc
{
    /**
     * 全局唯一标识,服务注册时用到
     * @Required
     */
    public $serviceName;

    /**
     * 服务名
     * 与config/topphpServer.php里的 servers.name 对应
     * @Required
     */
    public $serverName;

    /**
     * 服务协议,目前仅支持 jsonrpc
     * @Required
     * @Enum({"jsonrpc","http-jsonrpc"})
     */
    public $protocol;

    /**
     * 发布到服务中心,如果不写的话会去配置文件中的nodes节点
     * @Enum({"consul"})
     */
    public $publish;
}
```
##### 定义服务提供者配置文件

```php
<?php
use Topphp\TopphpSwoole\server\HttpServer;
use Topphp\TopphpSwoole\server\RpcServer;
use Topphp\TopphpSwoole\server\WebSocketServer;

return [
    'mode'    => SWOOLE_PROCESS,                  // 运行模式为SWOOLE_PROCESS时支持热重启.
    'servers' => [
        [
            'type'      => HttpServer::class,
            'name'      => 'gateway',
            'host'      => '0.0.0.0',                       // 监听地址
            'port'      => 9501,                            // 监听端口
            'sock_type' => SWOOLE_SOCK_TCP,
            'options'   => [
                // 开启websocket服务时设为true
                'open_websocket_protocol' => false
            ]
        ],
        [
            'type'      => RpcServer::class,
            'name'      => 'film-server',
            'host'      => '0.0.0.0',                       // 监听地址
            'port'      => 9502,                            // 监听端口
            'sock_type' => SWOOLE_SOCK_TCP,
            'options'   => []
        ],
        [
            'type'      => RpcServer::class,
            'name'      => 'film-server',
            'host'      => '0.0.0.0',                       // 监听地址
            'port'      => 9503,                            // 监听端口
            'sock_type' => SWOOLE_SOCK_TCP,
            'options'   => []
        ],
    ],
    'options' => [
        'pid_file'              => runtime_path() . 'topphp_swoole.pid',
        'log_file'              => runtime_path() . 'topphp_swoole.log',
        'daemonize'             => !env('APP_DEBUG'),   // 是否开启守护进程
        // Normally this value should be 1~4 times larger according to your cpu cores.
        'worker_num'            => swoole_cpu_num(),
        'task_worker_num'       => swoole_cpu_num(),    // 配置 Task 进程的数量。【默认值：未配置则不启动 task】
        'task_enable_coroutine' => true,
        'task_max_request'      => 1000000,
        'enable_static_handler' => true,
        'document_root'         => root_path('public'),
        'package_max_length'    => 20 * 1024 * 1024,
        'buffer_output_size'    => 10 * 1024 * 1024,
        'socket_buffer_size'    => 128 * 1024 * 1024,
        'max_request'           => 1000000,
        'max_wait_time'         => 60,
        'send_yield'            => true,
    ],
    // 客户端配置
    'clients' => [
        [
            'name'     => 'film-server',    //服务名称 和servers.name对应
            'balancer' => 'random',        //负载机制,暂时仅支持随机
            // 服务节点
            'nodes'    => [
                ['host' => '0.0.0.0', 'port' => 9502],
                ['host' => '127.0.0.1', 'port' => 9503]
            ],
            // 客户端配置项,详情看swoole文档
            'options' => []
        ]
    ],
];
```

#### Rpc消费端使用方法

```php
/** @var CinemaService $a */
$a = RpcConsumer::make(CinemaService::class);
$a->test1(211,2);
// {"id":"test1_5e628e799f89e","value":209}
```

##### 客户端调试可以用 `telent`

```shell
telnet 127.0.0.1 9502
```

###### 原始协议格式

```json
// 请求 method:name@id@函数名 组合成一个json字符串
{"jsonrpc":"2.0","method":"cinema-server@cinemaService@test1","params":[9,"11"],"id":123}

// 成功返回值
{"jsonrpc":"2.0","id":123,"result":-2}

// 失败返回值
{"jsonrpc":"2.0","id":2,"error":{"code":-32603,"message":"Method not found"}}
```

