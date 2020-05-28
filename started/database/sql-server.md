## Microsoft SqlServer 需要安装相关驱动:

#### 配置文件
`config/database.php`
```php
<?php

return [
    // 默认使用的数据库连接配置
    'default'         => env('DATABASE.DRIVER', 'sqlsrv'),

    // 自定义时间查询规则
    'time_query_rule' => [],

    // 自动写入时间戳字段
    // true为自动识别类型 false关闭
    // 字符串则明确指定时间字段类型 支持 int timestamp datetime date
    'auto_timestamp'  => true,

    // 时间字段取出后的默认时间格式
    'datetime_format' => 'Y-m-d H:i:s',

    // 数据库连接配置信息
    'connections'     => [
        'sqlsrv' => [
            // 数据库类型
            'type'              => env('DATABASE.TYPE', 'sqlsrv'),
            // 服务器地址
            'hostname'          => env('DATABASE.HOSTNAME', '127.0.0.1'),
            // 数据库名
            'database'          => env('DATABASE.DATABASE', ''),
            // 用户名
            'username'          => env('DATABASE.USERNAME', 'root'),
            // 密码
            'password'          => env('DATABASE.PASSWORD', ''),
            // 端口
            'hostport'          => env('DATABASE.HOSTPORT', '3306'),
            // 数据库连接参数
            'params'            => [],
            // 数据库编码默认采用utf8
            'charset'           => env('DATABASE.CHARSET', 'utf8'),
            // 数据库表前缀
            'prefix'            => env('DATABASE.PREFIX', ''),

            // 数据库部署方式:0 集中式(单一服务器),1 分布式(主从服务器)
            'deploy'            => 0,
            // 数据库读写是否分离 主从式有效
            'rw_separate'       => false,
            // 读写分离后 主服务器数量
            'master_num'        => 1,
            // 指定从服务器序号
            'slave_no'          => '',
            // 是否严格检查字段是否存在
            'fields_strict'     => true,
            // 是否需要断线重连
            'break_reconnect'   => false,
            // 监听SQL
            'trigger_sql'       => env('APP_DEBUG', true),
            // 开启字段缓存
            'fields_cache'      => false,
            // 字段缓存路径
            'schema_cache_path' => app()->getRuntimePath() . 'schema' . DIRECTORY_SEPARATOR,
        ],
        // 更多的数据库配置信息
    ],
];

```
#### 安装php扩展
```shell
pecl install sqlsrv
pecl install pdo_sqlsrv
```

#### 安装 odbc驱动
Mac环境:
```shell
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

brew tap microsoft/mssql-release https://github.com/Microsoft/homebrew-mssql-release

brew update

HOMEBREW_NO_ENV_FILTERING=1 ACCEPT_EULA=Y brew install msodbcsql17 mssql-tools
```

Ubuntu 安装 odbc:
```shell
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

#Download appropriate package for the OS version
#Choose only ONE of the following, corresponding to your OS version

#Ubuntu 16.04
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list

#Ubuntu 18.04
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > /etc/apt/sources.list.d/mssql-release.list

#Ubuntu 19.10
curl https://packages.microsoft.com/config/ubuntu/19.10/prod.list > /etc/apt/sources.list.d/mssql-release.list

exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql17
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y apt-get install mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo apt-get install unixodbc-dev
```