
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

2. 继承 `Topphp\TopphpSwoole\services\RpcService` 

3. 添加服务的容器依赖,配置 `config/service.php`

```php
<?php

use app\AppService;
use app\service\CinemaService;
use app\service\FilmService;

// 系统服务定义文件
// 服务在完成全局初始化之后执行
return [
    AppService::class,
    FilmService::class,
    CinemaService::class,
];
```

>即可发布这个服务了：

```php
<?php
declare (strict_types=1);

namespace app\service;

use Topphp\TopphpSwoole\annotation\Rpc;
use Topphp\TopphpSwoole\services\RpcService;

/**
 * @Rpc(name="filmService",protocol="jsonrpc",server="film-server")
 */
class FilmService extends RpcService
{
    public static function test($a, $b)
    {
        return $a + $b;
    }
}
```

##### `@Rpc` 注解的参数解释

```php
<?php
/**
 * 凯拓软件 [临渊羡鱼不如退而结网,凯拓与你一同成长]
 * @package topphp-swoole
 * @date 2020/3/1 01:19
 * @author sleep <sleep@kaituocn.com>
 */

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
    public $id;

    /**
     * 服务名
     * 与config/topphpServer.php里的 servers.name 对应
     * @Required
     */
    public $name;

    /**
     * 服务协议,目前仅支持 jsonrpc
     * @Required
     * @Enum({"jsonrpc","http-jsonrpc"})
     */
    public $protocol;

    /**
     * 暂不支持
     * @Enum({"consul"})
     */
    public $publish;
}
```
##### 定义服务提供者配置文件

```php
<?php
/**
 * 凯拓软件 [临渊羡鱼不如退而结网,凯拓与你一同成长]
 * Project: topphp-skeleton
 * Date: 2020/2/4 16:13
 * Author: sleep <sleep@kaituocn.com>
 */

use Topphp\TopphpSwoole\server\HttpServer;
use Topphp\TopphpSwoole\server\RpcServer;
use Topphp\TopphpSwoole\server\WebSocketServer;

return [
    'mode'    => SWOOLE_PROCESS,                  // 运行模式为SWOOLE_PROCESS时支持热重启.
    'servers' => [
        [
            'type'      => HttpServer::class,
            'name'      => 'gateway',
            'host'      => env('SWOOLE_HOST', '127.0.0.1'), // 监听地址
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
            'host'      => env('SWOOLE_HOST', '127.0.0.1'), // 监听地址
            'port'      => 9502,                            // 监听端口
            'sock_type' => SWOOLE_SOCK_TCP,
            'options'   => []
        ],
        [
            'type'      => RpcServer::class,
            'name'      => 'cinema-server',
            'host'      => env('SWOOLE_HOST', '127.0.0.1'), // 监听地址
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
        'reactor_num'           => swoole_cpu_num(),
        'worker_num'            => swoole_cpu_num(),
        'task_worker_num'       => swoole_cpu_num(),
        'task_enable_coroutine' => true,
        'task_max_request'      => 3000,
        'enable_static_handler' => true,
        'document_root'         => root_path('public'),
        'package_max_length'    => 20 * 1024 * 1024,
        'buffer_output_size'    => 10 * 1024 * 1024,
        'socket_buffer_size'    => 128 * 1024 * 1024,
        'max_request'           => 3000,
        'max_wait_time'         => 60,
        'send_yield'            => true,
        'reload_async'          => true,
        'enable_coroutine'      => true,
    ],
];
```

##### 协议格式

```json
// 请求 method:name@id@函数名 组合成一个json字符串
{"jsonrpc":"2.0","method":"cinema-server@cinemaService@test1","params":[9,"11"],"id":123}

// 成功返回值
{"jsonrpc":"2.0","id":123,"result":-2}
// 失败返回值
{"jsonrpc":"2.0","id":2,"error":{"code":-32603,"message":"Method not found"}}
```

##### 客户端调试可以用 `telent`

```shell
telnet 127.0.0.1 9502
```
