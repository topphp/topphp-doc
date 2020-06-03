## BaseModel Trait类

`BaseModel`为你提供常用的公共 **增删改查** 操作，以及一些便捷的快速查询的方法。主要包含如下几大类方法：

[新增](/composer/topphp-generate/BaseModel/add.md)

[修改/编辑](/composer/topphp-generate/BaseModel/edit.md)

[字段自增/自减](/composer/topphp-generate/BaseModel/step.md)

[删除/软删除](/composer/topphp-generate/BaseModel/delete.md)

[查询/链式操作](/composer/topphp-generate/BaseModel/select.md)

[查询一条](/composer/topphp-generate/BaseModel/select/one.md)

[查询所有](/composer/topphp-generate/BaseModel/select/all.md)

[limit查询](/composer/topphp-generate/BaseModel/select/limit.md)

[关联查询](/composer/topphp-generate/BaseModel/select/join.md)

[分页及其他](/composer/topphp-generate/BaseModel/page.md)

> 捕获`BaseModel`类中的错误，我们可以在模型`Dao`层通过`$this->getModelError()`方法进行获取。