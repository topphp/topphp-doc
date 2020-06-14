## Cookie

## 概述

ThinkPHP采用`think\facade\Cookie`类提供Cookie支持。

## 配置

配置文件位于配置目录下的`cookie.php`文件，无需手动初始化，系统会在调用之前自动进行`Cookie`初始化工作。

支持的参数及默认值如下：

```php
// cookie 保存时间
'expire'    => 0,
// cookie 保存路径
'path'      => '/',
// cookie 有效域名
'domain'    => '',
//  cookie 启用安全传输
'secure'    => false,
// httponly设置
'httponly'  => '',
```

## 基本操作

### 设置

```php
// 设置Cookie 有效期为 3600秒
Cookie::set('name', 'value', 3600);
```

`Cookie`数据不支持数组，如果需要请自行序列化后存入。

### 永久保存

```php
// 永久保存Cookie
Cookie::forever('name', 'value');
```

### 删除

```php
//删除cookie
Cookie::delete('name');
```

### 读取

```php
// 读取某个cookie数据
Cookie::get('name');
// 获取全部cookie数据
Cookie::get();
```

## 助手函数

系统提供了`cookie`助手函数用于基本的`cookie`操作，例如：

```php
// 设置
cookie('name', 'value', 3600);

// 获取
echo cookie('name');

// 删除
cookie('name', null);
```



