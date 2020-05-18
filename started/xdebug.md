# 单元测试

> 注意swoole和xdebug不可同时开启.此扩展仅支持Apache,Nginx,php-fpm环境下使用.
swoole环境下请安装 [sdebug](/started/xdebug.md)

#### 安装xdebug扩展
```shell
pecl install xdebug

# 查找php.ihi路径
php --ini

# 添加xdebug扩展
[xdebug]
zend_extension="xdebug.so"
xdebug.remote_enable = 1
xdebug.remote_connect_back = 1
xdebug.remote_port = 9000
xdebug.max_nesting_level = 500
```

>也可以参考下这个链接的配置方法
https://www.jetbrains.com/help/phpstorm/2019.3/configuring-xdebug.html?utm_campaign=PS&utm_medium=link&utm_source=product&utm_content=2019.3

>xdebug官网
https://xdebug.org/docs/install


