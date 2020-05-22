## 响应

> 响应（`Response`）对象用于动态响应客户端请求，控制发送给用户的信息。通常用于输出数据给客户端或者浏览器。

`ThinkPHP`的`Response`响应对象由`think\Response`类或者子类完成，ThinkPHP的响应输出是自动的（命令行模式除外），最终会调用`Response`对象的`send`方法完成输出。

> `Response`类不能直接实例化，必须使用`Response::make()`静态方式创建，建议直接使用系统提供的助手函数完成。





