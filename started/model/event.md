## 模型事件

模型事件是指在进行模型的查询和写入操作的时候触发的操作行为。

> 模型事件只在调用模型的方法生效，使用查询构造器操作是无效的

模型支持如下事件：

| 事件 | 描述 | 事件方法名 |
| :--- | :--- | :--- |
| after\_read | 查询后 | onAfterRead |
| before\_insert | 新增前 | onBeforeInsert |
| after\_insert | 新增后 | onAfterInsert |
| before\_update | 更新前 | onBeforeUpdate |
| after\_update | 更新后 | onAfterUpdate |
| before\_write | 写入前 | onBeforeWrite |
| after\_write | 写入后 | onAfterWrite |
| before\_delete | 删除前 | onBeforeDelete |
| after\_delete | 删除后 | onAfterDelete |
| before\_restore | 恢复前 | onBeforeRestore |
| after\_restore | 恢复后 | onAfterRestore |

注册的回调方法支持传入一个参数（当前的模型对象实例），但支持依赖注入的方式增加额外参数。

> 如果`before_write`、`before_insert`、`before_update`、`before_delete`事件方法中返回`false`或者抛出`think\exception\ModelEventException`异常的话，则不会继续执行后续的操作。

## 模型事件定义

最简单的方式是在模型类里面定义静态方法来定义模型的相关事件响应。

```php
<?php
namespace app\model;

use think\Model;
use app\model\Profile;

class User extends Model
{
    public static function onBeforeUpdate($user)
    {
    	if ('thinkphp' == $user->name) {
        	return false;
        }
    }
    
    public static function onAfterDelete($user)
    {
		Profile::destroy($user->id);
    }
}
```

参数是当前的模型对象实例，支持使用依赖注入传入更多的参数。

