## composer.json文件示例

```json
{
    "name": "topphp/component-builder",
    "type": "library",
    "description": "组件生成器",
    "keywords": [
        "topphp",
        "component-builder"
    ],
    "homepage": "https://github.com/topphp/component-builder",
    "license": "MIT",
    "authors": [
        {
            "name": "topphp",
            "email": "sleep@kaituocn.com",
            "homepage": "https://github.com/topphp",
            "role": "Developer"
        }
    ],
    "require": {
        "php": "~7.2"
    },
    "require-dev": {
        "phpunit/phpunit": ">=8.0",
        "squizlabs/php_codesniffer": "^3.0"
    },
    "autoload": {
        "psr-4": {
            "topphp\\componentBuilder\\": "src"
        },
        "files": [
            "src/helper.php"
        ]
    },
    "autoload-dev": {
        "psr-4": {
            "topphp\\Test\\": "tests"
        }
    },
    "scripts": {
        "test": "phpunit",
        "check-style": "phpcs src tests",
        "fix-style": "phpcbf src tests",
        "post-root-package-install": []
    },
    "extra": {
        "branch-alias": {
            "dev-master": "1.0-dev"
        },
        "think": {
            "services": [
                //注册的服务写这里
            ],
            "config": {
                // 配置文件
                "builder": "src/config/builder.php"
            }
        }
    },
    "minimum-stability": "dev",
    "config": {
        "sort-packages": true
    }
}
```

> extra中   
> branch-alias为当前组件版本参数  
> think为框架配置参数,"config"参数中的配置会自动生成配置文件到 项目根目录 `config`中  
> ![](/assets/WX20200108-155443@2x.png)


### 添加命令行执行方法
![](/assets/1.png)

添加与composer.json中定义的services命名空间一样的文件Service.php 继承 `\think\Service`
实现boot()方法

```
// 把对应的命令行类加载到下面数组内
$this->commands([Server::class]);

```

命令行代码如下
![](/assets/2.png)


### 组件自定义助手函数
```

"files": [
    "src/helper.php"
]

```

`helper.php`

```php

<?php

function testhaha()
{
    echo 'haha';
}

```

