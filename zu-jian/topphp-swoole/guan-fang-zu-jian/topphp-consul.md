# 服务中心客户端
> topphp提供了一个consul组件,基于 `topphp/topphp-client` 提供的`http` 客户端进行的封装,依赖于`thinkphp6`。
> topphp-swoole 组件已经内置该组件

#### 单独安装
```shell
composer require topphp/topphp-consul
```
#### 配置
> 配置文件 `config/consul.php`

```php
<?php
return [
    'uri' => '127.0.0.1:8500'
];
```

#### 使用
> 获取相应客户端对象
```php
<?php
$this->agent = App::make(Agent::class);
$this->kv = App::make(KV::class);

$this->health = App::make(Health::class);
//返回一个 `ConsulResponse` 对象,可以通过 `->json()` 获取相应json数据
$services = $this->health->service($serviceName);

```