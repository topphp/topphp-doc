# php出现Module 'xxx' already loaded
php网页总是出现Module 'xxx' already loaded这样的提示语,是因为这个模块被重复加载了,删除多余的`extension=xxx.so`就可以了

# Swoole 短名未关闭
```shell
[ERROR] Swoole short name have to disable before start server, please set swoole.use_shortname = 'Off' into your php.ini.
```
您需要在您的 php.ini 配置文件增加 swoole.use_shortname = 'Off' 配置项
