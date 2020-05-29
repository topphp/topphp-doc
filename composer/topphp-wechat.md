# topphp-wechat

[![Latest Version on Packagist][ico-version]][link-packagist]
[![Software License][ico-license]](LICENSE.md)
[![Build Status][ico-travis]][link-travis]
[![Coverage Status][ico-scrutinizer]][link-scrutinizer]
[![Quality Score][ico-code-quality]][link-code-quality]
[![Total Downloads][ico-downloads]][link-downloads]

> 本组件是基于easywechat封装的一个组件.使用方法和原始功能几乎一模一样,只是增加了swoole环境下的支持.加入了容器注入,在整个生命周期内为单例.

# 使用教程

##### 1.入门
> 我们知道,使用`easywechat`时要先初始化,像这样:
```php
$config = [
    'app_id' => 'wx3cf0f39249eb0exx',
    'secret' => 'f1c242f4f28f735d4687abb469072axx',
];
$app = Factory::officialAccount($config);
```

> 而在`topphp`中,已经进行了容器注入,所以在实例化时要这样写:
 
```php
$wechat = $this->app->get(WeChat::class);
$config = $this->app->config->get('wechat');
$app = $wechat::officialAccount($config);

# 也可以这样用:
$app = WeChat::officialAccount($config);
# 但是要注意,这样是多例模式
```

##### 2.组件安装后会在骨架工程的`config` 目录自动生成`wechat.php`文件,里面则是 `easywechat` 的配置参数.

## 具体使用方式参见  [easywechat官网](https://www.easywechat.com/)

#### 版本
现代的PHP组件都使用语义版本方案(http://semver.org), 版本号由三个点(.)分数字组成(例如:1.13.2).第一个数字是主版本号,如果PHP组件更新破坏了向后兼容性,会提升主版本号.
第二个数字是次版本号,如果PHP组件小幅更新了功能,而且没有破坏向后兼容性,会提升次版本号.
第三个数字(即最后一个数字)是修订版本号,如果PHP组件修正了向后兼容的缺陷,会提升修订版本号.

## Structure
> 组件结构

```
bin/        
build/
docs/
config/
src/
tests/
vendor/
```


## Install

Via Composer

``` bash
$ composer require topphp/topphp-wechat
```

## Change log

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Testing

``` bash
$ composer test
```

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) and [CODE_OF_CONDUCT](CODE_OF_CONDUCT.md) for details.

## Security

If you discover any security related issues, please email sleep@kaituocn.com instead of using the issue tracker.

## Credits

- [topphp][link-author]
- [All Contributors][link-contributors]

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.

[ico-version]: https://img.shields.io/packagist/v/topphp/topphp-wechat.svg?style=flat-square
[ico-license]: https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square
[ico-travis]: https://img.shields.io/travis/topphp/topphp-wechat/master.svg?style=flat-square
[ico-scrutinizer]: https://img.shields.io/scrutinizer/coverage/g/topphp/topphp-wechat.svg?style=flat-square
[ico-code-quality]: https://img.shields.io/scrutinizer/g/topphp/topphp-wechat.svg?style=flat-square
[ico-downloads]: https://img.shields.io/packagist/dt/topphp/topphp-wechat.svg?style=flat-square

[link-packagist]: https://packagist.org/packages/topphp/topphp-wechat
[link-travis]: https://travis-ci.org/topphp/topphp-wechat
[link-scrutinizer]: https://scrutinizer-ci.com/g/topphp/topphp-wechat/code-structure
[link-code-quality]: https://scrutinizer-ci.com/g/topphp/topphp-wechat
[link-downloads]: https://packagist.org/packages/topphp/topphp-wechat
[link-author]: https://github.com/topphp
[link-contributors]: ../../contributors
