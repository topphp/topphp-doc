## 重定向

可以使用`redirect`助手函数进行重定向

```php
<?php
namespace app\controller;

class Index
{
    public function hello()
    {
        return redirect('http://www.thinkphp.cn');
    }
}
```

### 重定向传参

如果是站内重定向的话，可以支持URL组装，有两种方式组装URL，第一种是直接使用完整地址（`/`打头）

```php
redirect('/index/hello/name/thinkphp');
```

如果你需要自动生成URL地址，应该在调用之前调用url函数先生成最终的URL地址。

```php
redirect((string) url('hello',['name' => 'think']));
```

还可以支持使用`with`方法附加`Session`闪存数据重定向。

```php
<?php
namespace app\controller;

class Index
{
    public function index()
    {
        return redirect('/hello')->with('name','thinkphp');
    }
    
    public function hello()
    {
    	$name = session('name');
        return 'hello,'.$name.'!';
    }    
}
```

从示例可以看到重定向隐式传值使用的是`Session`闪存数据隐式传值，并且**仅在下一次请求有效**，再次访问重定向地址的时候无效。

### 记住请求地址

在很多时候，我们重定向的时候需要记住当前请求地址（为了便于跳转回来），我们可以使用`remember`方法记住重定向之前的请求地址。

下面是一个示例，我们第一次访问`index`操作的时候会重定向到`hello`操作并记住当前请求地址，然后操作完成后到`restore`方法，`restore`方法则会自动重定向到之前记住的请求地址，完成一次重定向的回归，回到原点！（再次刷新页面又可以继续执行）

```php
<?php
namespace app\controller;

class Index
{
    public function index()
    {
        // 判断session完成标记是否存在
        if (session('?complete')) {
            // 删除session
            session('complete', null);
            return '重定向完成，回到原点!';
        } else {
            // 记住当前地址并重定向
            return redirect('hello')
                ->with('name', 'thinkphp')
                ->remember();
        }
    }

    public function hello()
    {
        $name = session('name');
        return 'hello,' . $name . '! <br/><a href="/index/index/restore">点击回到来源地址</a>';
    }

    public function restore()
    {
        // 设置session标记完成
        session('complete', true);
        // 跳回之前的来源地址
        return redirect()->restore();
    }
}
```

### SendMsg 发送重定向响应

>推荐使用`TopPHP`的`SendMsg`类直接向客户端发送重定向的header Location请求，此种方式可以完美支持`topphp/topphp-swoole`组件环境下的重定向。

```php
$url = "http://www.domain.com";
return SendMsg::jsonJump($url);
```

如果是站内地址可以：

```php
$url = request()->domain() . "/admin";
return SendMsg::jsonJump($url);
```


