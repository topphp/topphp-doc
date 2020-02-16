# sdebug
> 该扩展支持在swoole环境下断点调试.不可和xdebug同时开启,php版本必须  >=7.0<=7.3

基于 xdebug2.7\([https://github.com/xdebug/xdebug](https://github.com/xdebug/xdebug)\)

### 注意事项

为了避免 swoole 的检测 xdebug 警告, 扩展注册的名称是 sdebug, 如果想使用 Phpunit CodeCoverage , 需要手动把检测 xdebug 的判断修改成 sdebug  
修改:  
`vendor/phpunit/php-code-coverage/src/Driver/Xdebug.php:38行`

```php
if (!\extension_loaded('sdebug')) {
    throw new RuntimeException('This driver requires Xdebug');
}
```

修改:  
`vendor/sebastian/environment/src/Runtime.php:176行`

```php
public function hasXdebug(): bool
{
    return ($this->isPHP() || $this->isHHVM()) && \extension_loaded('sdebug');
}
```

> 单步调试: 如果php不是7.3的, 建议使用 sdebug\_2\_6\([https://github.com/mabu233/sdebug/tree/sdebug](https://github.com/mabu233/sdebug/tree/sdebug)\), sdebug\_2\_7 可能需要与phpstorm2019搭配使用

#### 第一步

```shell
git clone https://github.com/swoole/sdebug.git
```

#### 第二步 编译

```shell
cd sdebug
./rebuild.sh


$ php -v
PHP 7.2.25 (cli) (built: Nov 22 2019 10:27:28) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.2.25, Copyright (c) 1999-2018, by Zend Technologies
    with Sdebug v2.7.3-dev, Copyright (c) 2002-2019, by Derick Rethans
```

#### 截图演示

##### ![](/assets/WX20200206-134340@2x.png)

##### 单元测试![](/assets/phpunit.png)



