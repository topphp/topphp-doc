## 删除与软删除

### 配置

通常使用`ThinkPHP`的软删除功能需要`use SoftDelete;`，但是如果你选择使用`TopPHP`的`BaseModel`类的相关方法，就不需要再次引用`ThinkPHP`的`use SoftDelete;`了，我们内置方法已经对软删除进行了处理。