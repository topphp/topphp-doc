# 单元测试

>先安装xdebug扩展

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

