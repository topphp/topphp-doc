## 内置规则

系统内置了一些常用的验证规则，可以完成大部分场景的验证需求，包括：

* [格式验证类](https://www.kancloud.cn/manual/thinkphp6_0/1037629#_6)
* [长度和区间验证类](https://www.kancloud.cn/manual/thinkphp6_0/1037629#_232)
* [字段比较类](https://www.kancloud.cn/manual/thinkphp6_0/1037629#_333)
* [filter验证](https://www.kancloud.cn/manual/thinkphp6_0/1037629#filter_403)
* [正则验证](https://www.kancloud.cn/manual/thinkphp6_0/1037629#_409)
* [上传验证](https://www.kancloud.cn/manual/thinkphp6_0/1037629#_444)
* [其它验证](https://www.kancloud.cn/manual/thinkphp6_0/1037629#_466)

> 验证规则严格区分大小写

## 格式验证类

格式验证类的验证规则如果在使用静态方法调用的时候需要加上`is`（以`number`验证为例，需要使用`isNumber()`）。

> ### require

验证某个字段必须，例如：

```php
'name'=>'require'
```

> 如果验证规则没有添加`require`就表示没有值的话不进行验证

> 由于`require`属于PHP保留字，所以在使用方法验证的时候必须使用`isRequire`或者`must`方法调用。

> ### number

验证某个字段的值是否为纯数字（采用`ctype_digit`验证，不包含负数和小数点），例如：

```php
'num'=>'number'
```

> ### integer

验证某个字段的值是否为整数（采用`filter_var`验证），例如：

```php
'num'=>'integer'
```

> ### float

验证某个字段的值是否为浮点数字（采用`filter_var`验证），例如：

```php
'num'=>'float'
```

> ### boolean 或者 bool

验证某个字段的值是否为布尔值（采用`filter_var`验证），例如：

```php
'num'=>'boolean'
```

> ### email

验证某个字段的值是否为email地址（采用`filter_var`验证），例如：

```php
'email'=>'email'
```

> ### array

验证某个字段的值是否为数组，例如：

```php
'info'=>'array'
```

> ### accepted

验证某个字段是否为为 yes, on, 或是 1。这在确认"服务条款"是否同意时很有用，例如：

```php
'accept'=>'accepted'
```

> ### date

验证值是否为有效的日期，例如：

```php
'date'=>'date'
```

会对日期值进行`strtotime`后进行判断。

> ### alpha

验证某个字段的值是否为纯字母，例如：

```php
'name'=>'alpha'
```

> ### alphaNum

验证某个字段的值是否为字母和数字，例如：

```php
'name'=>'alphaNum'
```

> ### alphaDash

验证某个字段的值是否为字母和数字，下划线`_`及破折号`-`，例如：

```php
'name'=>'alphaDash'
```

> ### chs

验证某个字段的值只能是汉字，例如：

```php
'name'=>'chs'
```

> ### chsAlpha

验证某个字段的值只能是汉字、字母，例如：

```php
'name'=>'chsAlpha'
```

> ### chsAlphaNum

验证某个字段的值只能是汉字、字母和数字，例如：

```php
'name'=>'chsAlphaNum'
```

> ### chsDash

验证某个字段的值只能是汉字、字母、数字和下划线\_及破折号-，例如：

```php
'name'=>'chsDash'
```

> ### cntrl

验证某个字段的值只能是控制字符（换行、缩进、空格），例如：

```php
'name'=>'cntrl'
```

> ### graph

验证某个字段的值只能是可打印字符（空格除外），例如：

```php
'name'=>'graph'
```

> ### print

验证某个字段的值只能是可打印字符（包括空格），例如：

```php
'name'=>'print'
```

> ### lower

验证某个字段的值只能是小写字符，例如：

```php
'name'=>'upper'
```

> ### upper

验证某个字段的值只能是大写字符，例如：

```
'name'
=
>
'upper'
```

> ### space

验证某个字段的值只能是空白字符（包括缩进，垂直制表符，换行符，回车和换页字符），例如：

```php
'name'=>'space'
```

> ### xdigit

验证某个字段的值只能是十六进制字符串，例如：

```php
'name'=>'xdigit'
```

> ### activeUrl

验证某个字段的值是否为有效的域名或者IP，例如：

```php
'host'=>'activeUrl'
```

> ### url

验证某个字段的值是否为有效的URL地址（采用`filter_var`验证），例如：

```php
'url'=>'url'
```

> ### ip

验证某个字段的值是否为有效的IP地址（采用`filter_var`验证），例如：

```php
'ip'=>'ip'
```

支持验证ipv4和ipv6格式的IP地址。

> ### dateFormat:format

验证某个字段的值是否为指定格式的日期，例如：

```php
'create_time'=>'dateFormat:y-m-d'
```

> ### mobile

验证某个字段的值是否为有效的手机，例如：

```php
'mobile'=>'mobile'
```

> ### idCard

验证某个字段的值是否为有效的身份证格式，例如：

```php
'id_card'=>'idCard'
```

> ### macAddr

验证某个字段的值是否为有效的MAC地址，例如：

```php
'mac'=>'macAddr'
```

> ### zip

验证某个字段的值是否为有效的邮政编码，例如：

```php
'zip'=>'zip'
```



