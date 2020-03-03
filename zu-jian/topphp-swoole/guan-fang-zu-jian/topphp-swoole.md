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

组件支持thinkphp的 事件机制,在骨架项目中 `app/subscribe` 目录可以自定义扩展swoole的全部事件回调.
`BaseServer.php`
`HttpServer.php`
`RpcServer.php`
`TcpServer.php`
`WebSocketServer.php`
