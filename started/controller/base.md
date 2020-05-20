## 基础控制器

大多数情况下，我们建议给你的控制器继承一个基础控制器。

默认安装后，系统提供了一个`app\BaseController`基础控制器类，你可以对该基础控制器进行修改。

> 基础控制器的位置可以随意放置，只需要注意更改命名空间即可。

该基础控制器仅仅提供了控制器验证功能，并注入了`think\App`和`think\Request`对象，因此你可以直接在控制器中使用`app`和`request`属性调用`think\App`和`think\Request`对象实例，下面是一个例子：

```php
namespace app\controller;

use app\BaseController;

class Index extends BaseController
{
    public function index()
    {
        $action = $this->request->action();
        $path = $this->app->getBasePath();
    }
}
```
> TopPHP在此基础控制器的基础上进行了优化与扩展。

对于一个常规项目来讲，难免少不了后台管理模块和前台应用模块，所以TopPHP骨架默认为多应用模式，自带`admin`应用与`index`应用，同时鉴于不同应用间业务是相互隔离的，为了相互解耦，TopPHP规范在对应的应用`controller`目录增加`Base.php`抽象类，该类继承于`app\BaseController`，可以沿用`app\BaseController`的所有公共特性的同时，还可以增加当前应用的独有公共特性。


### 控制器验证

基础控制器提供了数据验证功能，使用如下：

```php
namespace app\controller;

use app\BaseController;
use think\exception\ValidateException;

class Index extends BaseController
{
    public function index()
    {
        try {
            $this->validate( [
                'name'  => 'thinkphp',
                'email' => 'thinkphp@qq.com',
            ],  'app\index\validate\User');
        } catch (ValidateException $e) {
            // 验证失败 输出错误信息
            dump($e->getError());
        }
    }
}
```

> 该示例使用了验证器功能，具体可以参考验证章节的验证器部分，这里暂时不做展开。

如果需要批量验证，可以改为：

```php
namespace app\controller;

use app\BaseController;
use think\exception\ValidateException;

class Index extends BaseController
{
    // 开启批量验证
    protected $batchValidate = true;

    public function index()
    {
        try {
            $this->validate( [
                'name'  => 'thinkphp',
                'email' => 'thinkphp@qq.com',
            ],  'app\index\validate\User');
        } catch (ValidateException $e) {
            // 验证失败 输出错误信息
            dump($e->getError());
        }
    }
}
```



