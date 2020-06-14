## 多语言

ThinkPHP内置通过`\think\facade\Lang`类提供多语言支持，如果你的应用涉及到国际化的支持，那么可以定义相关的语言包文件。任何字符串形式的输出，都可以定义语言常量。

## 开启和加载语言包

默认系统会加载默认语言包，但如果需要多语言自动侦测及自动切换，你需要在全局的中间件定义文件中添加中间件定义：

```php
'think\middleware\LoadLangPack',
```

默认情况下，系统载入的是配置的默认语言包，并且不会自动侦测当前系统的语言。多语言相关的设置在`lang.php`配置文件中进行设置。

默认语言由`default_lang`配置参数设置，系统默认设置为：

```php
// 默认语言
'default_lang'    => 'zh-cn',
```

启用中间件后，系统会自动侦测和多语言自动切换，可以在配置文件设置自动侦测的多语言变量名：

```php
// 自动侦测的GET变量名
'detect_var' => 'lang',
```

开启自动侦测后会会首先检查请求的URL或者Cookie中是否包含语言变量，然后根据`HTTP_ACCEPT_LANGUAGE`自动识别当前语言（并载入对应的语言包）。

如果在自动侦测语言的时候，希望设置允许的语言列表，不在列表范围的语言则仍然使用默认语言，可以配置：

```php
// 设置允许的语言
'allow_lang_list'    =>    ['zh-cn', 'en-us']
```

如果希望使用`Cookie`保存语言，可以设置

```php
// 使用Cookie保存
'use_cookie' => true,
// Cookie保存变量
'cookie_var' => 'think_lang',
```

设置后，自动检测的语言会通过`Cookie`记录下来，下次则直接通过`Cookie`判断语言。

## 语言变量定义

语言变量的定义，只需要在需要使用多语言的地方，写成：

```php
Lang::get('add user error');
// 使用系统封装的助手函数
lang('add user error');
```

也就是说，字符串信息要改成`Lang::get`方法来表示。

语言定义一般采用英语来描述。

## 语言文件定义

自动加载的应用语言文件位于：

```php
// 单应用模式
app\lang\当前语言.php
// 多应用模式
app\应用\lang\当前语言.php
```

如果你还需要加载其他的语言包，可以通过`extend_list`设置，例如：

```php
'extend_list'    =>    [
    'zh-cn'    => [
        app()->getBasePath() . 'lang\zh-cn\app.php',
        app()->getBasePath() . 'lang\zh-cn\core.php',
    ],
]
```

> 目前核心框架仅内置`zh-cn`语言包，如果需要其它语言的提示，可以通过扩展语言包的方式自行加载。

ThinkPHP语言文件定义采用返回数组方式：

```php
return [
     'hello thinkphp'  => '欢迎使用ThinkPHP',
     'data type error' => '数据类型错误',
];
```

通常多语言的使用是在控制器里面，但是模型类的自动验证功能里面会用到提示信息，这个部分也可以使用多语言的特性。

如果使用了多语言功能的话（假设，我们在当前语言包里面定义了' lang\_var'=&gt;'标题必须！'），就可以使用下面的字符串来替代原来的错误提示。

```php
{%lang_var}
```

如果要在模板中输出语言变量不需要在控制器中赋值，可以直接使用模板引擎特殊标签来直接输出语言定义的值：

```php
{$Think.lang.lang_var}
```

可以输出当前语言包里面定义的 `lang_var`语言定义。

## 变量传入支持

语言包定义的时候支持传入变量，有两种方式

使用命名绑定方式，例如：

```php
'file_format'    =>    '文件格式: {:format},文件大小：{:size}',
```

在模板中输出语言字符串的时候传入变量值即可：

```php
{:lang('file_format',['format' => 'jpeg,png,gif,jpg','size' => '2MB'])}
```

第二种方式是使用格式字串，如果你需要使用第三方的翻译工具，建议使用该方式定义变量。

```php
'file_format'    =>    '文件格式: %s,文件大小：%d',
```

在模板中输出多语言的方式更改为：

```php
{:lang('file_format',['jpeg,png,gif,jpg','2MB'])}
```

## 语言分组

首先你需要在lang.php配置文件中开启语言分组，

```php
// 开启多语言分组
'allow_group'    =>    true
```

然后你可以在定义多语言的时候使用分组定义

```php
return [
    'user'    =>    [
         'welcome'  => '欢迎回来',
         'login' => '用户登录',
         'logout' => '用户登出',
    ]
];
```

然后使用下面的方式获取多语言变量

```php
Lang::get('user.login');
lang('user.login');
```



