## 组件开发流程说明

#### 组件开发必备目录结构

![list 目录](/assets/mu-lu-jie-gou.jpg)

![list 目录](/assets/mu-lu-jie-gou-topphp.jpg)

~~~
开发准备：我们需要在topphp组件目录下进行对应的组件开发
        a、开发前需从gitlab上拉取骨架项目（topphp-skeleton）
        b、开发前需从gitlab上拉取开发文档项目（topphp-doc）
        c、在骨架目录同级目录创建topphp组件目录
        d、在gitlab上的topphp分组下创建你自己的组件git项目，命名以topphp-xxx为标准
        e、拉取你新创建的组件项目或者拉取其他需要二次开发的组件到你本地的topphp目录下
~~~

#### 本地构建新的组件（基于已存在的组件二次开发，此项可忽略）

> composer create-project topphp/component-builder=dev-master 组件名

![list 目录](/assets/build-utils.png)

~~~
注意事项：构建新的组件时，切记使用英文输入法填写信息，并关注填写完组件名称后，填写命名空间时提示的默认命名空间名称。
        查看默认的命名空间名称是否为 Topphp\Topphpxxx 结构，如果不是，请手动填写为正确的上述结构
~~~

> 确认构建完成的目录结构是否完全，不完整，需重新构建

![list 目录](/assets/build-detail.jpg)

> 创建你的开发分支进行开发，不要直接在master分支上进行开发

#### 可以根据需求开发你的组件啦，下面关于写单元测试的一些说明

~~~
1、在自主二开的单元测试模块组件未完成前，暂时可以使用phpunit进行单元测试
    -- 切换目录到你的组件开发目录下
    -- 全文件测试：./vendor/bin/phpunit tests/文件名.php
    -- 指定方法名：./vendor/bin/phpunit tests/文件名.php --filter 方法名
    -- 如果提示权限不足：chmod +x ./vendor/bin/phpunit 修改权限
    -- chmod +x /home/wwwroot/topphp/topphp-rsa/vendor/phpunit/phpunit/phpunit 修改vendor目录phpunit权限
    -- 关于PHPStrom的单元测试配置，详见单元测试一章节
2、topphp-testing组件开发完成后，安装并使用topphp-testing组件进行单元测试
~~~

#### 组件开发完成，需要在骨架进行生产调试

* 注意修改骨架composer.json文件，添加 "minimum-stability": "dev" 参数，如下图说明

![list 目录](/assets/skeleton-care.png)

* 骨架引入组件 composer require 组件名=dev-master 注意开发版，未发布前，必须加 =dev-master

![list 目录](/assets/skeleton-require.png)

* 骨架调用组件，命名空间统一使用 Topphp\Topphpxxx\xxx ，如下图

![list 目录](/assets/skeleton-use.png)

#### 生产调试没问题后，提交git，并提交merge到master分支，进行组件评审，全员通过后方可合并到master

#### 最后发布到 composer packagist 库 https://packagist.org/