## 时间比较

> 框架内置了常用的时间查询方法，并且可以自动识别时间字段的类型，所以无论采用什么类型的时间字段，都可以统一使用本章的时间查询用法。

### 使用`whereTime`方法

`whereTime`方法提供了日期和时间字段的快捷查询，示例如下：

```php
// 大于某个时间
Db::name('user')
    ->whereTime('birthday', '>=', '1970-10-1')
    ->select();
// 小于某个时间
Db::name('user')
    ->whereTime('birthday', '<', '2000-10-1')
    ->select();
// 时间区间查询
Db::name('user')
    ->whereTime('birthday', 'between', ['1970-10-1', '2000-10-1'])
    ->select();
// 不在某个时间区间
Db::name('user')
    ->whereTime('birthday', 'not between', ['1970-10-1', '2000-10-1'])
    ->select();
```

还可以使用下面的时间表达式进行时间查询

```php
// 查询两个小时内的博客
Db::name('blog')
	->whereTime('create_time','-2 hours')
    ->select();
```

## 查询某个时间区间

针对时间的区间查询，系统还提供了`whereBetweenTime/whereNotBetweenTime`快捷方法。

```php
// 查询2017年上半年注册的用户
Db::name('user')
    ->whereBetweenTime('create_time', '2017-01-01', '2017-06-30')
    ->select();
    
// 查询不是2017年上半年注册的用户
Db::name('user')
    ->whereNotBetweenTime('create_time', '2017-01-01', '2017-06-30')
    ->select();
```

## 查询某年

查询今年注册的用户

```php
Db::name('user')
    ->whereYear('create_time')
    ->select();   
```

查询去年注册的用户

```php
Db::name('user')
    ->whereYear('create_time', 'last year')
    ->select();   
```

查询某一年的数据使用

```php
// 查询2018年注册的用户
Db::name('user')
    ->whereYear('create_time', '2018')
    ->select();    
```

## 查询某月

查询本月注册的用户

```php
Db::name('user')
    ->whereMonth('create_time')
    ->select();   
```

查询上月注册用户

```php
Db::name('user')
    ->whereMonth('create_time','last month')
    ->select();   
```

查询2018年6月注册的用户

```php
Db::name('user')
    ->whereMonth('create_time', '2018-06')
    ->select();    
```

## 查询某周

查询本周数据

```php
Db::name('user')
    ->whereWeek('create_time')
    ->select();    
```

查询上周数据

```php
Db::name('user')
    ->whereWeek('create_time', 'last week')
    ->select();    
```

查询指定某天开始的一周数据

```php
// 查询2019-1-1到2019-1-7的注册用户
Db::name('user')
    ->whereWeek('create_time', '2019-1-1')
    ->select();    
```

## 查询某天

查询当天注册的用户

```php
Db::name('user')
    ->whereDay('create_time')
    ->select();   
```

查询昨天注册的用户

```php
Db::name('user')
    ->whereDay('create_time', 'yesterday')
    ->select();   
```

查询某天的数据使用

```php
// 查询2018年6月1日注册的用户
Db::name('user')
    ->whereDay('create_time', '2018-06-01')
    ->select();    
```

## 时间字段区间比较

可以支持对两个时间字段的区间比较

```php
// 查询有效期内的活动
Db::name('event')
	->whereBetweenTimeField('start_time', 'end_time')
    ->select();
```

上面的查询相当于

```php
// 查询有效期内的活动
Db::name('event')
	->whereTime('start_time', '<=', time())
    ->whereTime('end_time', '>=', time())
    ->select();
```

## 自定义时间查询规则

你可以通过在数据库配置文件中设置`time_query_rule`添加自定义的时间查询规则，

```php
'time_query_rule'    =>    [
    'hour'    =>    ['1 hour ago', 'now'],
],
```

  


