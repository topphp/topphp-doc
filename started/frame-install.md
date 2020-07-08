### 服务器要求
* PHP >= 7.2
* Swoole PHP 扩展 >= 4.5，并关闭了 Short Name
* OpenSSL PHP 扩展
* JSON PHP 扩展
* PDO PHP 扩展 （如需要使用到 MySQL 客户端）
* Redis PHP 扩展 （如需要使用到 Redis 客户端）

### Composer 安装
>请确保你的运行环境已经安装好了 Composer。

>composer create-project topphp/topphp-skeleton=dev-master

### GitHub 安装
>https://github.com/topphp/topphp-skeleton

### 运行服务
###### 普通方式启动

```
    php think run
```

###### swoole方式启动

```
    1、需要先安装 `composer require topphp/topphp-swoole`
    2、php think server
```

### 与swoole组件存在兼容性问题的扩展
* xhprof
* xdebug
* blackfire
* trace
* uopz