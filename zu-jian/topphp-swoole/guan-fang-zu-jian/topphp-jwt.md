# topphp-jwt

[![Latest Version on Packagist][ico-version]][link-packagist]
[![Software License][ico-license]](LICENSE.md)
[![Total Downloads][ico-downloads]][link-downloads]

>这是一个JWT工具，Json Web Token 跨语言支持的无状态web数据传输标准。常应用于登录验证。

## 包含方法

 - JwtHelper::handler();// 返回原始JWT对象句柄
 - JwtHelper::generateToken($id,$data,$expTime,$fromUrl,$toUrl);// 生成Token（支持设置过期时间与设置-1永久不过期以及校验签发者URL与接受者URL）
 - JwtHelper::generateRefreshToken($id,$data,$fromUrl,$toUrl);// 生成带有refreshToken的Token集合
 - JwtHelper::verifyToken($token,$isAll,$allowUrl);// 验证Token，支持返回原始全部JWT数据与校验签发者URL
 - JwtHelper::refreshToken($refreshToken,$data);// 根据refreshToken刷新Token，支持更新data中的数据
 - JwtHelper::refreshAllToken($refreshToken,$data);// 根据refreshToken刷新Token，返回值包含新的refreshToken，支持更新data中的数据

## 组件结构


```
config/     
src/     
tests/
vendor/
```


## 安装

``` bash
    composer require topphp/topphp-jwt
```

## 用法

```php
    命名空间引用（建议直接使用助手类）：
        use Topphp\TopphpJwt\JwtHelper;
    调用方式有两种：
        1、通过原始JWT单例调用
            JWT::getInstance()->setJti(10001)->setData(["test"=>"中文"])->createToken();
        2、通过助手类直接调用
            JwtHelper::handler()->setJti(10001)->setData(["test"=>"中文"])->createToken();
        3、助手类直接提供优化过的完善的快捷方法【推荐】
            JwtHelper::generateToken(10001, ["test"=>"中文"], time() + 7200);
    注意事项：
        1、默认建议使用HMAC方式进行签名加密，如果对于数据加密需求比较高，提供RSA方式签名加密，只需要修改配置use_rsa为true
        2、使用RSA签名，配置文件topphpJwt中公私钥地址如果为空，默认获取根目录下 pem 中的公钥地址，可结合topphp-rsa组件创建公私钥
        3、默认的助手类已经提供了大部分常用场景所需的方法，更多用法可以参看单元测试文件和对应的官方文档
```

## 修改日志

有关最近更改的内容的详细信息，请参阅更改日志（[CHANGELOG](CHANGELOG.md)）。

## 测试

``` bash
    ./vendor/bin/phpunit tests/JwtTest.php
```

## 贡献

详情请参阅贡献（[CONTRIBUTING](CONTRIBUTING.md)）和行为准则（[CODE_OF_CONDUCT](CODE_OF_CONDUCT.md)）。


## 安全

如果您发现任何与安全相关的问题，请发送电子邮件至sleep@kaitoocn.com，而不要使用问题跟踪器。

## 信用

- [topphp][link-author]
- [All Contributors][link-contributors]

## 许可证

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.

[ico-version]: https://img.shields.io/packagist/v/topphp/component-builder.svg?style=flat-square
[ico-license]: https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square
[ico-travis]: https://img.shields.io/travis/topphp/component-builder/master.svg?style=flat-square
[ico-scrutinizer]: https://img.shields.io/scrutinizer/coverage/g/topphp/component-builder.svg?style=flat-square
[ico-code-quality]: https://img.shields.io/scrutinizer/g/topphp/component-builder.svg?style=flat-square
[ico-downloads]: https://img.shields.io/packagist/dt/topphp/component-builder.svg?style=flat-square

[link-packagist]: https://packagist.org/packages/topphp/component-builder
[link-travis]: https://travis-ci.org/topphp/component-builder
[link-scrutinizer]: https://scrutinizer-ci.com/g/topphp/component-builder/code-structure
[link-code-quality]: https://scrutinizer-ci.com/g/topphp/component-builder
[link-downloads]: https://packagist.org/packages/topphp/component-builder
[link-author]: https://github.com/topphp
[link-contributors]: ../../contributors
