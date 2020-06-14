## 验证场景

验证场景仅针对验证器有效，独立验证不存在验证场景的概念

验证器支持定义场景，并且验证不同场景的数据，例如：

```php
namespace app\validate;

use think\Validate;

class User extends Validate
{
    protected $rule =   [
        'name'  => 'require|max:25',
        'age'   => 'number|between:1,120',
        'email' => 'email',    
    ];
    
    protected $message  =   [
        'name.require' => '名称必须',
        'name.max'     => '名称最多不能超过25个字符',
        'age.number'   => '年龄必须是数字',
        'age.between'  => '年龄只能在1-120之间',
        'email'        => '邮箱格式错误',    
    ];
    
    protected $scene = [
        'edit'  =>  ['name','age'],
    ];    
}
```

然后可以在验证方法中制定验证的场景

```php
$data = [
    'name'  => 'thinkphp',
    'age'   => 10,
    'email' => 'thinkphp@qq.com',
];

try {
    validate(app\validate\User::class)
        ->scene('edit')
        ->check($data);
} catch (ValidateException $e) {
    // 验证失败 输出错误信息
    dump($e->getError());
}
```

可以单独为某个场景定义方法（方法的命名规范是`scene`+场景名），并且对某些字段的规则重新设置，例如：

* 注意：场景名不区分大小写，且在调用的时候不能将驼峰写法转为下划线

```php
namespace app\validate;

use think\Validate;

class User extends Validate
{
    protected $rule =   [
        'name'  => 'require|max:25',
        'age'   => 'number|between:1,120',
        'email' => 'email',    
    ];
    
    protected $message  =   [
        'name.require' => '名称必须',
        'name.max'     => '名称最多不能超过25个字符',
        'age.number'   => '年龄必须是数字',
        'age.between'  => '年龄只能在1-120之间',
        'email'        => '邮箱格式错误',    
    ];
    
    // edit 验证场景定义
    public function sceneEdit()
    {
    	return $this->only(['name','age'])
        	->append('name', 'min:5')
            ->remove('age', 'between')
            ->append('age', 'require|max:100');
    }    
}
```

主要方法说明如下：

| 方法名 | 描述 |
| :--- | :--- |
| only | 场景需要验证的字段 |
| remove | 移除场景中的字段的部分验证规则 |
| append | 给场景中的字段需要追加验证规则 |

如果对同一个字段进行多次规则补充（包括移除和追加），必须使用下面的方式：

```php
remove('field', ['rule1','rule2'])
// 或者
remove('field', 'rule1|rule2')
```

下面的方式会导致rule1规则remove不成功

```php
remove('field', 'rule1')
->remove('field', 'rule2')
```

  




