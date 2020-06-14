## 异常处理

`TopPHP`默认已接管`ThinkPHP`的异常处理，`DEBUG`模式下与`ThinkPHP`的异常显示并无异处，生产环境，如果是ajax请求会直接返回`json`数据，如果是其他请求将会返回类似如下页面：

![error](/assets/error.png)

如果你需要对于异常处理页面进行展示优化，可以自定义修改`extend/lib/TopExceptionTmpl.php`文件。

如果你想在代码执行时使用`throw`方式抛出异常，建议直接调用`SendMsg::jsonThrow()`方法。详细可以查看[Code响应](/started/response/code.md)章节。