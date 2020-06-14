## 异常处理

`TopPHP`默认已接管`ThinkPHP`的异常处理，`DEBUG`模式下与`ThinkPHP`的异常显示并无异处，生产环境，如果是ajax请求会直接返回`json`数据，如果是其他请求将会返回类似如下页面：

![error](/assets/error.png)

