## 连接数据库

如果应用需要使用数据库，必须配置数据库连接信息，数据库的配置文件有多种定义方式。

## 配置文件

在全局或者应用配置目录（不清楚配置目录位置的话参考配置章节）下面的`database.php`中（后面统称为数据库配置文件）配置下面的数据库参数：

```php
return [
    'default'    =>    'mysql',
    'connections'    =>    [
        'mysql'    =>    [
            // 数据库类型
            'type'        => 'mysql',
            // 服务器地址
            'hostname'    => '127.0.0.1',
            // 数据库名
            'database'    => 'thinkphp',
            // 数据库用户名
            'username'    => 'root',
            // 数据库密码
            'password'    => '',
            // 数据库连接端口
            'hostport'    => '',
            // 数据库连接参数
            'params'      => [],
            // 数据库编码默认采用utf8
            'charset'     => 'utf8',
            // 数据库表前缀
            'prefix'      => 'think_',
        ],
    ],
];
```

新版采用多类型的方式配置，方便切换数据库。

`default`配置用于设置默认使用的数据库连接配置。  
`connections`配置具体的数据库连接信息，`default`配置参数定义的连接配置必须要存在。

`type`参数用于指定数据库类型

| type | 数据库 |
| :--- | :--- |
| mysql | MySQL |
| sqlite | SqLite |
| pgsql | PostgreSQL |
| sqlsrv | SqlServer |
| mongo | MongoDb |
| oracle | Oracle |

每个应用可以设置独立的数据库连接参数，通常直接更改`default`参数即可：

```php
return [
    'default'    =>    'admin', 
];
```

### 连接参数

可以针对不同的连接需要添加数据库的连接参数（具体的连接参数可以参考PHP手册），内置采用的参数包括如下：

```php
PDO::ATTR_CASE              => PDO::CASE_NATURAL,
PDO::ATTR_ERRMODE           => PDO::ERRMODE_EXCEPTION,
PDO::ATTR_ORACLE_NULLS      => PDO::NULL_NATURAL,
PDO::ATTR_STRINGIFY_FETCHES => false,
PDO::ATTR_EMULATE_PREPARES  => false,
```

在数据库配置文件中设置的`params`参数中的连接配置将会和内置的设置参数合并，如果需要使用长连接，并且返回数据库的小写列名，可以在数据库配置文件中增加下面的定义：

```php
'params' => [
    \PDO::ATTR_PERSISTENT   => true,
    \PDO::ATTR_CASE         => \PDO::CASE_LOWER,
],
```

> 你可以在`params`参数里面配置任何PDO支持的连接参数。

## 切换连接

我们可以在数据库配置文件中定义多个连接信息

```php
return [
    'default'    =>    'mysql',
    'connections'    =>    [
        'mysql'    =>    [
            // 数据库类型
            'type'        => 'mysql',
            // 服务器地址
            'hostname'    => '127.0.0.1',
            // 数据库名
            'database'    => 'thinkphp',
            // 数据库用户名
            'username'    => 'root',
            // 数据库密码
            'password'    => '',
            // 数据库连接端口
            'hostport'    => '',
            // 数据库连接参数
            'params'      => [],
            // 数据库编码默认采用utf8
            'charset'     => 'utf8',
            // 数据库表前缀
            'prefix'      => 'think_',
        ],
        'demo'    =>    [
            // 数据库类型
            'type'        => 'mysql',
            // 服务器地址
            'hostname'    => '127.0.0.1',
            // 数据库名
            'database'    => 'demo',
            // 数据库用户名
            'username'    => 'root',
            // 数据库密码
            'password'    => '',
            // 数据库连接端口
            'hostport'    => '',
            // 数据库连接参数
            'params'      => [],
            // 数据库编码默认采用utf8
            'charset'     => 'utf8',
            // 数据库表前缀
            'prefix'      => 'think_',
        ],
    ],
];
```

我们可以调用`Db::connect`方法动态配置数据库连接信息，例如：

```php
\think\facade\Db::connect('demo')
	->table('user')
    ->find();
```

> `connect`方法必须在查询的最开始调用，而且必须紧跟着调用查询方法，否则可能会导致部分查询失效或者依然使用默认的数据库连接。

动态连接数据库的`connect`方法仅对当次查询有效。

> 这种方式的动态连接和切换数据库比较方便，经常用于多数据库连接的应用需求。

## 模型类定义

如果某个模型类里面定义了`connection`属性的话，则该模型操作的时候会自动按照给定的数据库配置进行连接，而不是配置文件中设置的默认连接信息，例如：

```php
<?php
namespace app\index\model;

use think\Model;

class User extends Model
{
    protected $connection = 'demo';
}
```

> 需要注意的是，ThinkPHP的数据库连接是惰性的，所以并不是在实例化的时候就连接数据库，而是在有实际的数据操作的时候才会去连接数据库。

## 配置参数参考

下面是默认支持的数据库连接信息：

| 参数名 | 描述 | 默认值 |
| :--- | :--- | :--- |
| type | 数据库类型 | 无 |
| hostname | 数据库地址 | 127.0.0.1 |
| database | 数据库名称 | 无 |
| username | 数据库用户名 | 无 |
| password | 数据库密码 | 无 |
| hostport | 数据库端口号 | 无 |
| dsn | 数据库连接dsn信息 | 无 |
| params | 数据库连接参数 | 空 |
| charset | 数据库编码 | utf8 |
| prefix | 数据库的表前缀 | 无 |
| deploy | 数据库部署方式:0 集中式\(单一服务器\),1 分布式\(主从服务器\) | 0 |
| rw\_separate | 数据库读写是否分离 主从式有效 | false |
| master\_num | 读写分离后 主服务器数量 | 1 |
| slave\_no | 指定从服务器序号 | 无 |
| fields\_strict | 是否严格检查字段是否存在 | true |
| fields\_cache | 是否开启字段缓存 | false |
| schema\_cache\_path | 字段缓存目录 | 无 |
| trigger\_sql | 是否开启SQL监听 | true |
| auto\_timestamp | 自动写入时间戳字段 | false |
| query | 指定查询对象 | think\db\Query |

常用数据库连接参数（`params`）可以参考[PHP在线手册](http://php.net/manual/zh/pdo.constants.php)中的以`PDO::ATTR_`开头的常量。

如果同时定义了 参数化数据库连接信息 和 dsn信息，则会优先使用dsn信息。

> 如果是使用`pgsql`数据库驱动的话，请先导入`thinkphp/library/think/db/connector/pgsql.sql`文件到数据库执行。

## 断线重连

如果你使用的是长连接或者命令行，在超出一定时间后，数据库连接会断开，这个时候你需要开启断线重连才能确保应用不中断。

在数据库连接配置中设置：

```php
// 开启断线重连
'break_reconnect' => true,
```

开启后，系统会自动判断数据库断线并尝试重新连接。大多数情况下都能自动识别，如果在一些特殊的情况下或者某些数据库驱动的断线标识错误还没有定义，支持配置下面的信息：

```php
// 断线标识字符串
'break_match_str' => [
	'error with',
],
```

在`break_match_str`配置中加入你的数据库错误信息关键词。



