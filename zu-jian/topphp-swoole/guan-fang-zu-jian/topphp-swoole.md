topphp-swoole

### 简介


### 安装

### 使用

```shell
# 启动服务
php think server
```
#### 协程

`defer特性`,`WaitGroup 特性`,`Parallel 特性` 详见 [协程章节](/started/coroutine.md)

#### tcp服务


#### http服务


#### rpc服务
详见 [微服务-jsonrpc章节](/wei-fu-wu/jsonrpc.md)

#### websocket服务

### 事件机制
组件支持thinkphp的事件机制,在骨架项目中 `app/subscribe` 目录可以自定义扩展swoole的全部事件回调.
`BaseServer.php`
`HttpServer.php`
`RpcServer.php`
`TcpServer.php`
`WebSocketServer.php`
在 `app/enent.php` 文件中添加如下代码:

```php
<?php
// 事件定义文件
use app\subscribe\BaseServer;
use app\subscribe\HttpServer;
use app\subscribe\RpcServer;
use app\subscribe\TcpServer;
use app\subscribe\WebSocketServer;

return [
    'bind' => [
    ],

    'listen' => [
        'AppInit'  => [],
        'HttpRun'  => [],
        'HttpEnd'  => [],
        'LogLevel' => [],
        'LogWrite' => [],
    ],

    'subscribe' => [
        BaseServer::class,
        HttpServer::class,
        RpcServer::class,
        TcpServer::class,
        WebSocketServer::class
    ],
];

```

> 示例:

```php
<?php
declare (strict_types=1);

namespace app\subscribe;

use think\Event;
use Topphp\TopphpSwoole\server\TopServerEvent;

class HttpServer
{
    public function onRequest($event)
    {
        // todo 这里可以处理请求后的事件回调
    }

    public function subscribe(Event $event)
    {
        $event->listen(TopServerEvent::ON_REQUEST, [$this, 'onRequest']);
    }
}

```

### 配置文件
> 示例:

```php
<?php

use Topphp\TopphpSwoole\balancer\RandomBalancer;
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
            'name'      => 'cinema-server',
            'host'      => '0.0.0.0',                       // 监听地址
            'port'      => 9503,                            // 监听端口
            'sock_type' => SWOOLE_SOCK_TCP,
            'options'   => []
        ],
        [
            'type'      => WebSocketServer::class,
            'name'      => 'top-WebSocketServer',
            'host'      => '0.0.0.0',                        // 监听地址
            'port'      => 9504,                             // 监听端口
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

    'clients' => [
        [
            'name'     => 'film-server',
            'balancer' => RandomBalancer::class,
            'nodes'    => [
                ['host' => '0.0.0.0', 'port' => 9502, 'weight' => 0],
                ['host' => '127.0.0.1', 'port' => 9502, 'weight' => 0]
            ],
            'options'  => [],
            'pool'     => [
                'min_connections' => 1,
                'max_connections' => 100,
                'wait_timeout'    => 10,
                'connect_timeout' => 10,
                'max_idle_time'   => 60.0
            ]
        ],
        [
            'name'     => 'cinema-server',
            'balancer' => 'random',
            'nodes'    => [
                ['host' => '192.168.31.108', 'port' => 9503, 'weight' => 0],
                ['host' => '127.0.0.1', 'port' => 9503, 'weight' => 0]
            ],
            'options'  => [],
            'pool'     => [
                'min_connections' => 1,
                'max_connections' => 100,
                'wait_timeout'    => 10,
                'connect_timeout' => 10,
                'max_idle_time'   => 60.0
            ]
        ]
    ],
];
```