topphp-swoole

### 简介


### 安装

### 使用

```shell
# 启动服务
php think server
```
#### 协程

`defer特性`,`WaitGroup 特性`,`Parallel 特性` 详见 [协程章节](/kuai-su-ru-men/xie-cheng.md)

#### tcp服务


### http服务


### rpc服务
详见 [微服务-jsonrpc章节](/wei-fu-wu/jsonrpc.md)


### websocket服务

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
