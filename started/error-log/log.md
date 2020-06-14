## 日志处理

> `TopPHP`已对`ThinkPHP`的日志进行了扩展优化，我们推荐使用`TopPHP`提供的`topphp-log`组件。  
> 您可以查看[`topphp-log`组件](/composer/topphp-log.md)文档。`TopPHP`日志组件部分配置功能仍然沿用`ThinkPHP`的配置方式，所以我们仍为你提供以下`ThinkPHP`日志功能的文档。

`ThinkPHP`日志记录和写入由`\think\Log`类完成，通常我们使用`think\facade\Log`类进行静态调用。

由于日志记录了所有的运行错误，因此养成经常查看日志文件的习惯，可以避免和及早发现很多的错误隐患。

> 日志遵循`PSR-3`规范,除非是实时写入的日志,其它日志都是在当前请求结束的时候统一写入的 所以不要在日志写入之后使用`exit`等中断操作会导致日志写入失败。

## 日志配置

日志的配置文件是配置文件目录下的`log.php`文件，系统在进行日志写入之前会读取该配置文件进行初始化。

新版的日志配置支持多通道，默认配置如下：

```php
return [
    // 默认日志记录通道
    'default'      => 'file',
    // 日志记录级别
    'level'        => [],
    // 日志类型记录的通道 ['error'=>'email',...]
    'type_channel' => [],

    // 日志通道列表
    'channels'     => [
        'file' => [
            // 日志记录方式
            'type'        => 'File',
            // 日志保存目录
            'path'        => '',
            // 单文件日志写入
            'single'      => false,
            // 独立日志级别
            'apart_level' => [],
            // 最大日志文件数量
            'max_files'   => 0,
        ],
        // 其它日志通道配置
    ],
];
```

可以添加多个日志通道，每个通道可以设置不同的日志类型。日志配置参数根据不同的日志类型有所区别，内置的日志类型包括：`file`，日志类型使用`type`参数配置即可。

> 如果是自定义驱动，`type`的值则为自定义驱动的类名（包含命名空间）

日志的全局配置参数包含：

| 参数 | 描述 |
| :--- | :--- |
| default | 默认的日志通道 |
| level | 允许记录的日志级别 |
| type\_channel | 日志类型记录的通道 |

> 默认的日志类型是`File`方式，可以通过驱动的方式来扩展支持更多的记录方式。

文件类型日志的话，还支持下列配置参数：

| 参数 | 描述 |
| :--- | :--- |
| path | 日志存储路径 |
| file\_size | 日志文件大小限制（超出会生成多个文件） |
| apart\_level | 独立记录的日志级别 |
| time\_format | 时间记录格式 |
| single | 是否单一文件日志 |
| max\_files | 最大日志文件数（超过自动清理 ） |
| format | 日志输出格式 |
| realtime\_write | 是否实时写入 |

> 为了避免同一个目录下面的日志文件过多的性能问题，日志文件会自动生成日期子目录。

## 日志写入

### 手动记录

一般情况下，系统的错误日志记录是自动的，如果需要记录应用的业务日志或者额外的日志信息，就需要手动记录日志信息，Log类主要提供了2个方法用于记录日志。

| 方法 | 描述 |
| :--- | :--- |
| record\(\) | 记录日志信息到内存 |
| write\(\) | 实时写入一条日志信息 |

系统在请求结束后会自动调用`Log::save`方法统一进行日志信息写入。

`record`方法用法如下：

```php
Log::record('测试日志信息');
```

默认记录的日志级别是`info`，也可以指定日志级别：

```php
Log::record('测试日志信息，这是警告级别','notice');
```

采用`record`方法记录的日志信息不是实时保存的，如果需要实时记录的话，可以采用`write`方法，例如：

```php
Log::write('测试日志信息，这是警告级别，并且实时写入','notice');
```

你可以在日志通道配置开启实时写入，每次记录日志信息的时候就会实时写入。

```php
'file' => [
    // 日志记录方式
    'type'        => 'File',
    // 日志保存目录
    'path'        => '',
    // 单文件日志写入
    'single'      => false,
    // 独立日志级别
    'apart_level' => [],
    // 最大日志文件数量
    'max_files'   => 0,
    // 日志处理
    'processor'   => null,
    // 实时写入
    'realtime_write'    =>    true,
],
```

> 为避免内存溢出，在命令行下面执行的话日志信息会自动实时写入。

### 关闭日志

要关闭日志功能，可以调用`Log::close()`方法关闭本次请求的日志写入。

```php
// 关闭当前日志写入
Log::close();
```

### 日志级别

ThinkPHP对系统的日志按照级别来分类记录，按照`PSR-3`日志规范，日志的级别从低到高依次为：`debug`,`info`,`notice`,`warning`,`error`,`critical`,`alert`,`emergency`，ThinkPHP额外增加了一个`sql`日志级别仅用于记录`SQL`日志（并且仅当开启数据库调试模式有效）。

> 系统发生异常后记录的日志级别是`error`

系统提供了不同日志级别的快速记录方法，例如：

```php
Log::error('错误信息');
Log::info('日志信息');
```

还封装了一个助手函数用于日志记录，例如：

```php
trace('错误信息', 'error');
trace('日志信息', 'info');
```

事实上，你可以增加自定义的日志类型，例如：

```php
Log::diy('这是一个自定义日志类型');
```

也支持指定级别日志的写入，需要配置信息：

```php
return [
    // 日志记录级别，使用数组表示
    'level' => ['error', 'alert'],
];
```

上面的配置表示只记录`error`和`alert`级别的日志信息。

> 默认情况下是不会记录HTTP异常日志（避免受一些攻击的影响写入大量日志），除非你接管了系统的异常处理，重写了`report`方法。

### 上下文信息

日志可以传入上下文信息（数组），并且被替换到日志内容中，例如：

```php
Log::info('日志信息{user}', ['user' => '流年']);
```

实际写入日志的时候，`{user}`会被替换为流年。

### 独立日志

为了便于分析，`File`类型的日志还支持设置某些级别的日志信息单独文件记录，例如：

```php
return [
    'default'      => 'file',
    'channels'    =>    [
        'file'    =>    [
            'type'          => 'file', 
            // error和sql日志单独记录
            'apart_level'   =>  ['error','sql'],
        ],
    ],
];
```

设置后，就会单独生成`error`和`sql`两个类型的日志文件，主日志文件中将不再包含这两个级别的日志信息。

> 如果`apart_level`设置为`true`，则表示所有的日志类型都会独立记录。

### 单文件日志

默认情况下，日志是按照日期为目录，按天为文件生成的，但如果希望仅生成单个文件（方便其它的工具或者服务读取以及分析日志）。

```php
return [
    'default'      => 'file',
    'channels'    =>    [
        'file'    =>    [
            'type'          => 'file', 
        	'single'		=>	true,
            'file_size'   	=> 	1024*1024*10,    
        ],
    ],
];
```

开启生成单个文件后，`file_size`和`apart_level`参数依然有效，超过文件大小限制后，系统会自动生成备份日志文件。

默认的单文件日志名是`single.log`，如果需要更改日志文件名，可以设置

```php
return [
    'default'      => 'file',
    'channels'    =>    [
        'file'    =>    [
            'type'          => 'file', 
            'single'		=>	'single_file',
            'file_size'   	=> 	1024*1024*10,    
        ],
    ],
];
```

那么实际生成的日志文件名是`single_file.log`，如果设置了`apart_level`的话，可能还会生成`single_file_error.log`之类的日志。

> 单文件日志也支持`max_files`参数设置，因为单文件日志同样会生成多个日志备份文件而导致日志文件数据过大。

### 写入处理

日志支持写入回调处理，通过事件的方式处理。

```php
Event::listen('think\event\LogWrite', function($event) {
    if('file' == $event->channel) {
        $event->log['info'][] = 'test info';
    }
});
```

### 格式化日志信息

系统提供了两个参数用于日志信息的格式化，第一个是用于自定义时间显示格式的`time_format`，第二个是调整日志输出格式的`format`参数。

```php
return  [
    'default'      => 'file',
    'channels'    =>    [
        'file'    =>    [
            'type'          => 'file', 
            'json'	        =>	true
            'file_size'   	=> 	1024*1024*10,    
            'time_format'   =>    'Y-m-d H:i:s',
            'format'        =>    '[%s][%s]:%s',
        ],
    ],
];
```

### 清空日志

一旦执行`save`方法后，内存中的日志信息就会被自动清空，如果需要手动清空可以使用：

```php
Log::clear();
```

在清空日志方法之前，你可以使用`getLog`方法获取内存中的日志。

```php
// 获取全部日志
$logs = Log::getLog();
```

> 日志清空仅仅是清空内存中的日志。

### 日志自动清理

文件类型的日志支持自动清理。可以设置`max_files`参数，超过数量的最早日志将会自动删除。

例如，下面设置日志最多保存数量为30个

```php
return [
    'default'      => 'file',
    'channels'    =>    [
        'file'    =>    [
            'type'          => 'file', 
            'max_files'	=>	30,
            'file_size'   	=> 	1024*1024*10,    
        ],
    ],
];
```

> 设置`max_files`参数后，日志文件将不会分日期子目录存放。

### JSON格式日志

可以支持`JSON`格式记录文件日志，更加方便一些第三方日志分析工具进行日志分析。

在日志配置文件中，添加

```php
return [
    'default'      => 'file',
    'channels'    =>    [
        'file'    =>    [
            'type'          => 'file', 
            'json'	        =>	true
            'file_size'   	=> 	1024*1024*10,    
        ],
    ],
];
```

即可开启`JSON`格式记录，CLI命令行的日志记录同样有效。

使用JSON格式记录后，每次请求是一行JSON数据，但如果使用`Log::write`记录的日志是例外的单独一行JSON数据。

## 日志通道

你可以配置不同的日志通道，并且把不同的日志记录到不同的通道。

```php
Log::channel('email')->info('一条测试日志');
Log::channel('socket')->error('记录错误日志');
```

你可以配置不同的日志类型，记录到不同的日志通道，这样在记录日志的时候会自动选择对应的通道写入。

```php
return [
    'default'    =>    'file',
    'type_channel'    =>    [
        'error'    =>    'email',
        'sql'    =>    'sql',
    ],
    'channels'    =>    [
        'file'    =>    [
            'type'    =>    'file',
        ],
        'email'    =>    [
            'type'    =>    'email',
        ],
        'sql'    =>    [
            'type'    =>    'sql',
        ],
    ],
];
```

表示如果是`error`日志和`sql`日志，会分别记录到指定的通道。同时你还需要在日志配置文件中，添加`email`和`sql`日志通道的配置。核心只有`file`日志类型，其它的可能需要自己扩展或者安装扩展。

如果需要获取内存中的通道日志信息，可以使用

```php
// 获取某个日志通道的日志
$error = Log::getLog('file');
```

可以单独关闭某个通道的日志写入，只需要把日志通道的`close`配置参数设置为`true`，或者使用方法关闭。

```php
Log::close('file');
```

可以单独清空某个通道的日志（如果没有开启实时写入的话）

```php
Log::clear('file');
```

## 自定义驱动

如果需要自定义日志驱动，你需要实现`think\contract\LogHandlerInterface`接口。

```php
interface LogHandlerInterface
{
    /**
     * 日志写入接口
     * @access public
     * @param  array $log 日志信息
     * @return bool
     */
    public function save(array $log): bool;

}
```

  




