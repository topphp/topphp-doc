# php出现Module 'xxx' already loaded
#### php网页总是出现Module 'xxx' already loaded这样的提示语,是因为这个模块被重复加载了,删除 `php.ini` 中多余的`extension=xxx.so`就可以了

# Swoole 短名未关闭
```shell
[ERROR] Swoole short name have to disable before start server, please set swoole.use_shortname = 'Off' into your php.ini.
```
#### 您需要在您的 php.ini 配置文件增加 swoole.use_shortname = 'Off' 配置项,也可以通过以下的命令来启动服务:
```shell
php -d swoole.use_shortname=Off think server
```

#### 为什么注解@Inject引入`Dao`层查询数据每次都是重复的
> 不可以通过注入(`@Inject`)的方式引入`Dao`层。Inject的方式是单例，Dao层属于数据连接层，每次应该`new`来实例化，不然的话会每次查询都是上一次的数据内容。