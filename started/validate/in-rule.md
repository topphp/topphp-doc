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
>
> ### max:number

验证某个字段的值的最大长度，例如：

```php
'name'=>'max:25'
```

> 如果验证的数据是数组，则判断数组的长度。  
> 如果验证的数据是File对象，则判断文件的大小。
>
> ### min:number

验证某个字段的值的最小长度，例如：

```php
'name'=>'min:5'
```

> 如果验证的数据是数组，则判断数组的长度。  
> 如果验证的数据是File对象，则判断文件的大小。
>
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

## 字段比较类

> ### confirm

验证某个字段是否和另外一个字段的值一致，例如：

```php
'repassword'=>'require|confirm:password'
```

支持字段自动匹配验证规则，如`password`和`password_confirm`是自动相互验证的，只需要使用

```php
'password'=>'require|confirm'
```

会自动验证和`password_confirm`进行字段比较是否一致，反之亦然。

> ### different

验证某个字段是否和另外一个字段的值不一致，例如：

```php
'name'=>'require|different:account'
```

> ### eq 或者 = 或者 same

验证是否等于某个值，例如：

```php
'score'=>'eq:100'
'num'=>'=:100'
'num'=>'same:100'
```

> ### egt 或者 &gt;=

验证是否大于等于某个值，例如：

```php
'score'=>'egt:60'
'num'=>'>=:100'
```

> ### gt 或者 &gt;

验证是否大于某个值，例如：

```php
'score'=>'gt:60'
'num'=>'>:100'
```

> ### elt 或者 &lt;=

验证是否小于等于某个值，例如：

```php
'score'=>'elt:100'
'num'=>'<=:100'
```

> ### lt 或者 &lt;

验证是否小于某个值，例如：

```php
'score'=>'lt:100'
'num'=>'<:100'
```

> ### 字段比较

验证对比其他字段大小（数值大小对比），例如：

```php
'price'=>'lt:market_price'
'price'=>'<:market_price'
```

## filter验证

支持使用`filter_var`进行验证，例如：

```php
'ip'=>'filter:validate_ip'
```

## 正则验证

支持直接使用正则验证，例如：

```php
'zip'=>'\d{6}',
// 或者
'zip'=>'regex:\d{6}',
```

如果你的正则表达式中包含有`|`符号的话，必须使用数组方式定义。

```php
'accepted'=>['regex'=>'/^(yes|on|1)$/i'],
```

也可以实现预定义正则表达式后直接调用，例如在验证器类中定义regex属性

```php
namespace app\index\validate;

use think\Validate;

class User extends Validate
{
    protected $regex = [ 'zip' => '\d{6}'];
    
    protected $rule = [
        'name'  =>  'require|max:25',
        'email' =>  'email',
    ];

}
```

然后就可以使用

```php
'zip'	=>	'regex:zip',
```

## 上传验证

> ### file

验证是否是一个上传文件

> ### image:width,height,type

验证是否是一个图像文件，width height和type都是可选，width和height必须同时定义。

> ### fileExt:允许的文件后缀

验证上传文件后缀

> ### fileMime:允许的文件类型

验证上传文件类型

> ### fileSize:允许的文件字节大小

验证上传文件大小

## 其它验证

> ### token:表单令牌名称

表单令牌验证

> ### unique:table,field,except,pk

验证当前请求的字段值是否为唯一的，例如：

```php
// 表示验证name字段的值是否在user表（不包含前缀）中唯一
'name'   => 'unique:user',
// 验证其他字段
'name'   => 'unique:user,account',
// 排除某个主键值
'name'   => 'unique:user,account,10',
// 指定某个主键值排除
'name'   => 'unique:user,account,10,user_id',
```

如果需要对复杂的条件验证唯一，可以使用下面的方式：

```php
// 多个字段验证唯一验证条件
'name'   => 'unique:user,status^account',
// 复杂验证条件
'name'   => 'unique:user,status=1&account='.$data['account'],
```

> ### requireIf:field,value

验证某个字段的值等于某个值的时候必须，例如：

```php
// 当account的值等于1的时候 password必须
'password'=>'requireIf:account,1'
```

> ### requireWith:field

验证某个字段有值的时候必须，例如：

```php
// 当account有值的时候password字段必须
'password'=>'requireWith:account'
```

> ### requireWithout:field

验证某个字段没有值的时候必须，例如：

```php
// mobile和phone必须输入一个
'mobile' => 'requireWithout:phone',
'phone'  => 'requireWithout:mobile'
```

> ### requireCallback:callable

验证当某个callable为真的时候字段必须，例如：

```php
// 使用check_require方法检查是否需要验证age字段必须
'age'=>'requireCallback:check_require|number'
```

用于检查是否需要验证的方法支持两个参数，第一个参数是当前字段的值，第二个参数则是所有的数据。

```php
function check_require($value, $data){
    if(empty($data['birthday'])){
    	return true;
    }
}
```

只有check\_require函数返回true的时候age字段是必须的，并且会进行后续的其它验证。

