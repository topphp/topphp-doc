## 表单令牌

## 添加令牌`Token`验证

验证规则支持对表单的令牌验证，首先需要在你的表单里面增加下面隐藏域：

```php
<input type="hidden" name="__token__" value="{:token()}" />
```

也可以直接使用

```php
{:token_field()}
```

默认的令牌Token名称是`__token__`，如果需要自定义名称及令牌生成规则可以使用

```php
{:token_field('__hash__', 'md5')}
```

第二个参数表示token的生成规则，也可以使用闭包。

如果你没有使用默认的模板引擎，则需要自己生成表单隐藏域

```php
namespace app\controller;

use think\Request;
use think\facade\View;

class Index
{
    public function index(Request $request)
    {
        $token = $request->buildToken('__token__', 'sha1');
        View::assign('token', $token);
        return View::fetch();
    }
}
```

然后在模板表单中使用：

```php
<input type="hidden" name="__token__" value="{$token}" />
```

## AJAX提交

如果是AJAX提交的表单，可以将`token`设置在`meta`中

```php
<meta name="csrf-token" content="{:token()}">
```

或者直接使用

```php
{:token_meta()}
```

然后在全局Ajax中使用这种方式设置`X-CSRF-Token`请求头并提交：

```php
$.ajaxSetup({
    headers: {
        'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
    }
});
```

## 路由验证

然后在路由规则定义中，使用

```php
Route::post('blog/save','blog/save')->token();
```

如果自定义了`token`名称，需要改成

```php
Route::post('blog/save','blog/save')->token('__hash__');
```

令牌检测如果不通过，会抛出`think\exception\ValidateException`异常。

## 控制器验证

如果没有使用路由定义，可以在控制器里面手动进行令牌验证

```php
namespace app\controller;

use think\exception\ValidateException;
use think\Request;

class Index
{
    public function index(Request $request)
    {
        $check = $request->checkToken('__token__');
        
        if(false === $check) {
            throw new ValidateException('invalid token');
        }

        // ...
    }
}
```

提交数据默认获取`post`数据，支持指定数据进行`Token`验证。

```php
namespace app\controller;

use think\exception\ValidateException;
use think\Request;

class Index
{
    public function index(Request $request)
    {
        $check = $request->checkToken('__token__', $request->param());
        
        if(false === $check) {
            throw new ValidateException('invalid token');
        }

        // ...
    }
}
```

## 使用验证器验证

在你的验证规则中，添加`token`验证规则即可，例如，如果使用的是验证器的话，可以改为：

```php
protected $rule = [
        'name'  =>  'require|max:25|token',
        'email' =>  'email',
    ];
```

如果你的令牌名称不是`__token__`（假设是`__hash__`\)，验证器中需要改为：

```php
protected $rule = [
        'name'  =>  'require|max:25|token:__hash__',
        'email' =>  'email',
    ];
```



