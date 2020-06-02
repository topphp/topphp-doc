## topphp-generate 数据库实体类生成组件

该组件可快速生成 `ThinkPHP` 的模型类，命名统一为`xxxDao`，继承自 `app\model\entity\XXX.php`数据模型实体类，自动创建并`use BaseModel;`Trait类。

`BaseModel.php`为基础模型公共类，用于编写常用的增删改查公共方法，或者有关项目的模型公共方法。仅在不存在`BaseModel.php`文件时自动创建，以后再运行命令不会对此文件进行改变或删除，所以用户可自己更改里面的代码。


### 安装

```bash
composer require topphp/topphp-generate
```

## 使用方法

需要先配置数据库信息 `config/database.php` 后进行命令行生成

```shell
php think gen:db
注意事项：
    1、自动生成：在topphp骨架里，多应用情况下，gen:db组件会根据你是否创建了对应的应用模块（如admin）下的model的文件夹来判断是否自动创建对应的model文件
    2、创建规范：应用模块（如admin）下的model文件统一会继承app/model/entity下的模型实体类,并全部以Dao.php结尾（此Dao层主要用于编写数据库业务）
    3、添加新的表：如果开发过程中需要添加新的数据表，可以执行 php think gen:db 进行自动更新模型实体类与模型Dao
    4、特别说明：
          a、每次执行 php think gen:db 模型实体类都会重置一遍，所以请不要直接编辑操作模型实体类
          b、每次执行 php think gen:db 对于已存在的模型Dao不会清空代码，对于不存在的模型Dao会自动创建（例如中途添加新的数据表场景）。
          c、每次执行 php think gen:db 以后使用传统环境（apache或nginx）部署在Linux系统下的注意统一修改一次文件夹权限（如：chown -R www:www www.domain.com/）
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

