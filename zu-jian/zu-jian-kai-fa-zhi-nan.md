# 创建组件命令

```shell
composer create-project topphp/component-builder=版本 你的组件名字

# 例如:
composer create-project topphp/component-builder=dev-master topphp-testing
Installing topphp/component-builder (dev-master 63957ce7c3e9e8425c280c0fa4135e847a6d5ec3)
  - Installing topphp/component-builder (dev-master 63957ce): Cloning 63957ce7c3 from cache
Created project in topphp-testing
> Topphp\Install\Shell::init
请对组件进行配置
请输入你的组件名称(topphp/demo):topphp/topphp-testing
填写你的组件描述:单元测试组件
请设置你的软件许可证(MIT):
请填写应用命名空间 (Topphp\TopphpTesting):
需要安装 topthink/framework 组件吗? [n]:y
正在删除安装脚本命名空间...
正在删除安装脚本相关composer配置...
删除安装脚本目录
Loading composer repositories with package information
Updating dependencies (including require-dev)
Package operations: 29 installs, 0 updates, 0 removals
  - Installing sebastian/version (2.0.1): Loading from cache
  - Installing sebastian/resource-operations (2.0.1): Loading from cache
  - Installing sebastian/recursion-context (3.0.0): Loading from cache
  - Installing sebastian/object-reflector (1.1.1): Loading from cache
  - Installing sebastian/object-enumerator (3.0.3): Loading from cache
  - Installing sebastian/global-state (2.0.0): Loading from cache
  - Installing sebastian/exporter (3.1.2): Loading from cache
  - Installing sebastian/environment (4.2.3): Loading from cache
  - Installing sebastian/diff (3.0.2): Loading from cache
  - Installing sebastian/comparator (3.0.2): Loading from cache
  - Installing phpunit/php-timer (2.1.2): Loading from cache
  - Installing phpunit/php-text-template (1.2.1): Loading from cache
  - Installing phpunit/php-file-iterator (2.0.2): Loading from cache
  - Installing theseer/tokenizer (1.1.3): Loading from cache
  - Installing sebastian/code-unit-reverse-lookup (1.0.1): Loading from cache
  - Installing phpunit/php-token-stream (3.1.1): Loading from cache
  - Installing phpunit/php-code-coverage (6.1.4): Loading from cache
  - Installing doctrine/instantiator (1.3.0): Loading from cache
  - Installing symfony/polyfill-ctype (v1.13.1): Loading from cache
  - Installing webmozart/assert (1.6.0): Loading from cache
  - Installing phpdocumentor/reflection-common (2.0.0): Loading from cache
  - Installing phpdocumentor/type-resolver (1.0.1): Loading from cache
  - Installing phpdocumentor/reflection-docblock (5.0.0): Loading from cache
  - Installing phpspec/prophecy (v1.10.2): Loading from cache
  - Installing phar-io/version (2.0.1): Loading from cache
  - Installing phar-io/manifest (1.0.3): Loading from cache
  - Installing myclabs/deep-copy (1.9.5): Loading from cache
  - Installing phpunit/phpunit (7.5.20): Loading from cache
  - Installing squizlabs/php_codesniffer (3.5.4): Loading from cache
sebastian/global-state suggests installing ext-uopz (*)
phpunit/php-code-coverage suggests installing ext-xdebug (^2.6.0)
phpunit/phpunit suggests installing phpunit/php-invoker (^2.0)
phpunit/phpunit suggests installing ext-xdebug (*)
Writing lock file
Generating autoload files
Do you want to remove the existing VCS (.git, .svn..) history? [Y,n]? Y
```
#### 注意
> 交互输入必须使用英文半角输入法,否则会出现字符确实.

### README文件

> 至少要包含的信息

1.组件名称和描述

2.安装说明

3.测试说明

4.贡献方式说明

5.支持资源

6.作者信息

7.软件许可证

8.版本号![](/assets/version.png)
现代的PHP组件都使用语义版本方案(http://semver.org),版本号由三个点(.)分数字组成(例如:1.13.2).第一个数字是主版本号,如果PHP组件更新破坏了向后兼容性,会提升主版本号.
第二个数字是次版本号,如果PHP组件小幅更新了功能,而且没有破坏向后兼容性,会提升次版本号.
第三个数字(即最后一个数字)是修订版本号,如果PHP组件修正了向后兼容的缺陷,会提升修订版本号.

