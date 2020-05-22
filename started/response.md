## 响应

> 响应（`Response`）对象用于动态响应客户端请求，控制发送给用户的信息。通常用于输出数据给客户端或者浏览器。

`ThinkPHP`的`Response`响应对象由`think\Response`类或者子类完成，ThinkPHP的响应输出是自动的（命令行模式除外），最终会调用`Response`对象的`send`方法完成输出。

> `Response`类不能直接实例化，必须使用`Response::make()`静态方式创建，建议直接使用系统提供的助手函数完成。

### SendMsg Code响应类

> `SendMsg` Class 为 `TopPHP` 骨架自带组件类，在`think\Response`基础上进行了优化与改进。主要为开发者提供更便捷的客户端响应机制。

如果你是接口形式的项目，势必需要一套规范的响应数据结构返回给客户端，那么在`TopPHP`骨架里使用`SendMsg`就是你不二的选择。

`SendMsg` 默认返回的数据结构为json格式：

```
// 操作成功的响应示例
{"code":10000,"message":"success","data":{"id":"1"}}

// 操作失败的响应示例
{"code":40000,"message":"系统异常","data":[]}
```

> `SendMsg` 还支持`xml`,`jsonp`的响应输出，详细用法请参看 `Code` 响应章节。



