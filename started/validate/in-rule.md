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
>
> 由于`require`属于PHP保留字，所以在使用方法验证的时候必须使用`isRequire`或者`must`方法调用。
>
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
'name'=>'lower'
```

> ### upper

验证某个字段的值只能是大写字符，例如：

```php
'name'=>'upper'
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

## 长度和区间验证类

> ### in

验证某个字段的值是否在某个范围，例如：

```php
'num'=>'in:1,2,3'
```

> ### notIn

验证某个字段的值不在某个范围，例如：

```php
'num'=>'notIn:1,2,3'
```

> ### between

验证某个字段的值是否在某个区间，例如：

```php
'num'=>'between:1,10'
```

> ### notBetween

验证某个字段的值不在某个范围，例如：

```php
'num'=>'notBetween:1,10'
```

> ### length:num1,num2

验证某个字段的值的长度是否在某个范围，例如：

```php
'name'=>'length:4,25'
```

或者指定长度

```php
'name'=>'length:4'
```

> 如果验证的数据是数组，则判断数组的长度。  
> 如果验证的数据是File对象，则判断文件的大小。

> ### max:number

验证某个字段的值的最大长度，例如：

```php
'name'=>'max:25'
```

> 如果验证的数据是数组，则判断数组的长度。  
> 如果验证的数据是File对象，则判断文件的大小。

> ### min:number

验证某个字段的值的最小长度，例如：

```php
'name'=>'min:5'
```

> 如果验证的数据是数组，则判断数组的长度。  
> 如果验证的数据是File对象，则判断文件的大小。

> ### after:日期

验证某个字段的值是否在某个日期之后，例如：

```php
'begin_time' => 'after:2016-3-18',
```

> ### before:日期

验证某个字段的值是否在某个日期之前，例如：

```php
'end_time'   => 'before:2016-10-01',
```

> ### expire:开始时间,结束时间

验证当前操作（注意不是某个值）是否在某个有效日期之内，例如：

```php
'expire_time'   => 'expire:2016-2-1,2016-10-01',
```

> ### allowIp:allow1,allow2,...

验证当前请求的IP是否在某个范围，例如：

```php
'name'   => 'allowIp:114.45.4.55',
```

该规则可以用于某个后台的访问权限，多个IP用逗号分隔

> ### denyIp:allow1,allow2,...

验证当前请求的IP是否禁止访问，例如：

```php
'name'   => 'denyIp:114.45.4.55',
```

多个IP用逗号分隔

