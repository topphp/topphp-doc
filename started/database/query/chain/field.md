## field

`field`方法主要作用是标识要返回或者操作的字段，可以用于查询和写入操作。

## 用于查询

### 指定字段

在查询操作中`field`方法是使用最频繁的。

```php
Db::table('user')->field('id,title,content')->select();
```

这里使用field方法指定了查询的结果集中包含id,title,content三个字段的值。执行的SQL相当于：

```php
SELECT id,title,content FROM user

```

可以给某个字段设置别名，例如：

```php
Db::table('user')->field('id,nickname as name')->select();
```

执行的SQL语句相当于：

```php
SELECT id,nickname as name FROM user

```

### 使用SQL函数

可以在`fieldRaw`方法中直接使用函数，例如：

```php
Db::table('user')->fieldRaw('id,SUM(score)')->select();
```

执行的SQL相当于：

```php
SELECT id,SUM(score) FROM user

```

> 除了`select`方法之外，所有的查询方法，包括`find`等都可以使用`field`方法。

### 使用数组参数

`field`方法的参数可以支持数组，例如：

```php
Db::table('user')->field(['id','title','content'])->select();
```

最终执行的SQL和前面用字符串方式是等效的。

数组方式的定义可以为某些字段定义别名，例如：

```php
Db::table('user')->field(['id','nickname'=>'name'])->select();
```

执行的SQL相当于：

```php
SELECT id,nickname as name FROM user

```

### 获取所有字段

如果有一个表有非常多的字段，需要获取所有的字段（这个也许很简单，因为不调用field方法或者直接使用空的field方法都能做到）：

```php
Db::table('user')->select();
Db::table('user')->field('*')->select();
```

上面的用法是等效的，都相当于执行SQL：

```php
SELECT * FROM user

```

但是这并不是我说的获取所有字段，而是显式的调用所有字段（对于对性能要求比较高的系统，这个要求并不过分，起码是一个比较好的习惯），下面的用法可以完成预期的作用：

```php
Db::table('user')->field(true)->select();
```

`field(true)`的用法会显式的获取数据表的所有字段列表，哪怕你的数据表有100个字段。

### 字段排除

如果我希望获取排除数据表中的`content`字段（文本字段的值非常耗内存）之外的所有字段值，我们就可以使用field方法的排除功能，例如下面的方式就可以实现所说的功能：

```php
Db::table('user')->withoutField('content')->select();
```

则表示获取除了content之外的所有字段，要排除更多的字段也可以：

```php
Db::table('user')->withoutField('user_id,content')->select();
//或者用
Db::table('user')->withoutField(['user_id','content'])->select();
```

> 注意的是 字段排除功能不支持跨表和join操作。

## 用于写入

除了查询操作之外，`field`方法还有一个非常重要的安全功能--**字段合法性检测**。`field`方法结合数据库的写入方法使用就可以完成表单提交的字段合法性检测，如果我们在表单提交的处理方法中使用了：

```php
Db::table('user')->field('title,email,content')->insert($data);
```

即表示表单中的合法字段只有`title`,`email`和`content`字段，无论用户通过什么手段更改或者添加了浏览器的提交字段，都会直接屏蔽。因为，其他所有字段我们都不希望由用户提交来决定，你可以通过自动完成功能定义额外需要自动写入的字段。

> 在开启数据表字段严格检查的情况下，提交了非法字段会抛出异常，可以在数据库设置文件中设置：

> ```php
> // 关闭严格字段检查
> 'fields_strict'	=>	false,
> ```



