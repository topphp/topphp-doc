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

对于一个常规项目来讲，难免少不了后台管理模块和前台应用模块，所以TopPHP骨架默认为多应用模式，自带`admin`应用与`index`应用，同时鉴于不同应用间业务是相互隔离的，为了相互解耦，TopPHP规范在对应的应用`controller`目录增加`Base.php`抽象类，该类继承于`app\BaseController`，可以沿用`app\BaseController`的所有公共特性的同时，还可以增加当前应用的独有公共特性。下面是关系示例：

```php
declare(strict_types=1);

namespace app\admin\controller;

use app\BaseController;

abstract class Base extends BaseController
{
    /**
     * 定义业务中间件
     * @var array
     */
    protected $middleware = ['Check','Auth'];

    // Admin应用基础业务逻辑（所有方法需定义protected关键词）
}
```

> TopPHP规范应用下的所有控制器应继承于应用基础控制器抽象类`Base.php`。

```php
├─app           应用目录
│  ├─admin              应用目录
│  │  ├─common.php      函数文件
│  │  ├─controller      控制器目录
│  │  │  ├─Base.php     应用基础控制器抽象类
│  │  │  ├─Index.php    Index控制器
│  │  │  └─...          更多控制器定义
│  │  ├─model           模型目录
│  │  ├─view            视图目录
│  │  ├─config          配置目录
│  │  ├─middleware      中间件目录
│  │  ├─route           路由目录
│  │  ├─service         服务目录
│  │  ├─validate        验证器目录
│  │  └─ ...            更多类库目录
```

```php
declare(strict_types=1);

namespace app\admin\controller;

class Index extends Base
{
    /**
     * index
     * @return string
     */
    public function index()
    {
        return "Hello TopPHP!";
    }
}
```

### 控制器验证

ThinkPHP的基础控制器提供了数据验证功能，使用如下：

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

> TopPHP对此做了优化，提供自动验证器中间件，开发者无需关注如何验证的，只需要知道如何配置验证器类如`app\admin\validate\IndexCheck.php`即可。具体可以参考验证章节的验证器部分。

```php
namespace app\admin\validate;

use think\Validate;

class IndexCheck extends Validate
{

    // 注意：全部Topphp验证器文件名统一以Check结尾，否则不会生效

    /**
     * 定义验证规则
     * 格式：'字段名'    =>    ['规则1','规则2'...]
     *
     * @var array
     */
    protected $rule = [

    ];

    /**
     * 定义错误信息【1、使用TopPHP自带的Check验证器中间件支持数组定义（骨架已自动集成）；2、使用Tp6的注解验证器不支持数组形式定义】
     * 格式：'字段名.规则名'    =>    '错误信息'
     * 数组定义示例："username" => ['code' => 40000, 'message' => '请填写用户名'] 返回 {"code":40000,"message":"请填写用户名","data":[]}
     *
     * 注意：因为是严格模式，错误信息内容被限定为字符串，传int型会报错，数组形式的code码允许是int型
     *
     * @var array
     */
    protected $message = [

    ];

    /**
     * 定义验证场景【单独验证某个字段的写法：操作方法名(actionName)@字段名 => ['字段验证规则key']，注意 @ 后字段名区分大小写】
     * 格式：【非多层级控制器】1、'操作方法名(actionName)'    =>    ['字段1','字段2'...]
     *       【多层级控制器】2、'层级名(layered).操作方法名(actionName)'    =>    ['字段1','字段2'...]
     * 单独验证：配置好单独验证的场景【"index@username"=>['username']】后，直接在控制器调用 checkOneRequestParam("username",MethodEnum::POST); 方法即可
     *
     * @var array
     */
    protected $scene = [

    ];
}
```



