### 我们建议使用PHPstorm进行开发.

> 字体设为 `Source Code Pro`字体.  
> ![](/assets/WX20200206-155821@2x.png)

[PHPstorm配置文件下载](/assets/settings.zip)

> 选择要导入的配置选项,点击确定保存

![](/assets/settings.png)![](/assets/settings2.png)

> 快捷键想保留自己的话,把`key maps`选项关掉  
> ![](/assets/settings3.png)

我们保持代码的一致性,可以在代码格式化时不会出现git的差异,格式保持统一有利于团队协作.  
例如:等号对齐  
![](/assets/WX20200206-161139@2x.png)

### 安装Annotations 插件

在使用 `topphp` 便捷的注解时,仍需要 use 注解相对应的命名空间,`PHP Annotations`可提供注解命名空间自动补全，注解属性代码提醒，注解类跳转等便捷操作.

### 编码规范及要求

1.代码注释要写在目标代码的上一行,行尾不允许写注释.  
2.尽量使用严格模式 `declare(strict_types=1);`,函数加返回值,参数定义数据类型.

### 关闭 swoole.ini中配置的短名称

`swoole.use_shortname='Off'`

### composer 更换阿里镜像

```shell
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

# 取消全局配置
composer config -g --unset repos.packagist

#生产环境要执行如下命令
composer dump-autoload -o
```



