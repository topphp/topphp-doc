# topphp-generate

[![](https://img.shields.io/packagist/v/topphp/topphp-generate.svg?style=flat-square)](https://packagist.org/packages/topphp/topphp-generate)  
[![](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE.md)  
[![](https://img.shields.io/travis/topphp/topphp-generate/master.svg?style=flat-square)](https://travis-ci.org/topphp/topphp-generate)  
[![](https://img.shields.io/scrutinizer/coverage/g/topphp/topphp-generate.svg?style=flat-square)](https://scrutinizer-ci.com/g/topphp/topphp-generate/code-structure)  
[![](https://img.shields.io/scrutinizer/g/topphp/topphp-generate.svg?style=flat-square)](https://scrutinizer-ci.com/g/topphp/topphp-generate)  
[![](https://img.shields.io/packagist/dt/topphp/topphp-generate.svg?style=flat-square)](https://packagist.org/packages/topphp/topphp-generate)

# php文件生成器

1.可快速生成 `thinkphp` 的模型类,继承自 `app\model\BaseModel`,`BaseModel.php`只创建第一次,以后再运行命令不会对此文件进行改变或删除,所以用户可自己更改里面的代码

### 组件结构

```
bin/        
build/
docs/
config/
src/
tests/
vendor/
```

## 安装

Via Composer

```bash
$ composer require topphp/topphp-generate
```

## 使用方法
需要先配置数据库信息 `config/database.php` 后进行命令行生成

```shell
php think gen:db
```
> 程序会在 `app/model/entity`目录下创建实体类
> 需要注意的是该目录里的文件每次都会删除源文件后重新生成，所以不要手动修改目录下的任何文件，如果有特殊需求想重新该实体类，name应该在非本目录下的其他目录新建一个文件，并继承该实体类。

## 效果图
![](/assets/db.png)![](/assets/gendb1.png)

# 版本说明
现代的PHP组件都使用语义版本方案\([http://semver.org](http://semver.org)\), 版本号由三个点\(.\)分数字组成\(例如:1.13.2\).第一个数字是主版本号,如果PHP组件更新破坏了向后兼容性,会提升主版本号.  
第二个数字是次版本号,如果PHP组件小幅更新了功能,而且没有破坏向后兼容性,会提升次版本号.  
第三个数字\(即最后一个数字\)是修订版本号,如果PHP组件修正了向后兼容的缺陷,会提升修订版本号.

## Change log

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Testing

```bash
$ composer test
```

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) and [CODE\_OF\_CONDUCT](CODE_OF_CONDUCT.md) for details.

## Security

If you discover any security related issues, please email sleep@kaituocn.com instead of using the issue tracker.

## Credits

* [topphp](https://github.com/topphp)
* [All Contributors](../../contributors)

## License

The MIT License \(MIT\). Please see [License File](LICENSE.md) for more information.

