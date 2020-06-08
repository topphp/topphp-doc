## 删除与软删除

### 配置

通常使用`ThinkPHP`的软删除功能需要`use SoftDelete;`，但是如果你选择使用`TopPHP`的`BaseModel`类的相关方法，就不需要再次引用`ThinkPHP`的`use SoftDelete;`了，我们内置方法已经对软删除进行了处理。

> `TopPHP`推荐你将数据表时间字段设计为`timestamp`类型的`create_time`、`update_time`、`delete_time`字段。使用`BaseModel`的相关方法时，我们无需引用任何其他的类或方法都会自动触发`自动时间戳`功能和`软删除`功能。

如果你有自定义软删除字段名的需求，可以在对应的模型`Dao`中定义`protected`关键字的`$deleteTime`属性：

```php
namespace app\index\model;

use app\model\entity\User;

class UserDao extends User
{
    // 自定义软删除字段名
    protected $deleteTime = 'mydel_time';
    // 定义软删除字段类型，支持 timestamp datetime year date time int 数据库字段类型
    protected $deleteTimeType = 'timestamp';
}
```

> 默认软删除字段名为`delete_time`，自定义软删除字段名时，请定义该软删除字段类型，如果没定义，默认会通过数据表实体类的`schema`属性来自动识别。

软删除字段类型支持 `timestamp` `datetime` `year` `date` `time` `int` 数据库字段类型，如果你定义的软删除字段不是以上字段类型，将会抛出异常。

> 当数据表实体类的`schema`属性既不存在`delete_time`字段名，且我们也没自定义软删除字段信息时，使用`BaseModel`的`remove`方法将会自动识别为真实删除。

