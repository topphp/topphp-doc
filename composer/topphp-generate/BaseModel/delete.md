## 删除与软删除

### 配置

通常使用`ThinkPHP`的软删除功能需要`use SoftDelete;`，但是如果你选择使用`TopPHP`的`BaseModel`类的相关方法，就不需要再次引用`ThinkPHP`的`use SoftDelete;`了，我们内置方法已经对软删除进行了处理。

> `TopPHP`推荐你将数据表时间字段设计为`timestamp`类型的`create_time`、`update_time`、`delete_time`字段。使用`BaseModel`的相关方法时，我们无需引用任何其他的类或方法都会自动触发`自动时间戳`功能和`软删除`功能。

