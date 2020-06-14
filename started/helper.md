## 助手函数

系统为一些常用的操作方法封装了助手函数，便于使用，包含如下：

| 助手函数 | 描述 |
| :--- | :--- |
| abort | 中断执行并发送HTTP状态码 |
| app | 快速获取容器中的实例 支持依赖注入 |
| bind | 快速绑定对象实例 |
| cache | 缓存管理 |
| class\_basename | 获取类名\(不包含命名空间\) |
| class\_uses\_recursive | 获取一个类里所有用到的trait |
| config | 获取和设置配置参数 |
| cookie | Cookie管理 |
| download | 获取\think\response\Download对象实例 |
| dump | 浏览器友好的变量输出 |
| env | 获取环境变量 |
| event | 触发事件 |
| halt | 变量调试输出并中断执行 |
| input | 获取输入数据 支持默认值和过滤 |
| invoke | 调用反射执行callable 支持依赖注入 |
| json | JSON数据输出 |
| jsonp | JSONP数据输出 |
| lang | 获取语言变量值 |
| parse\_name | 字符串命名风格转换 |
| redirect | 重定向输出 |
| request | 获取当前Request对象 |
| response | 实例化Response对象 |
| session | Session管理 |
| token | 生成表单令牌输出 |
| trace | 记录日志信息 |
| trait\_uses\_recursive | 获取一个trait里所有引用到的trait |
| url | Url生成 |
| validate | 实例化验证器 |
| view | 渲染模板输出 |
| display | 渲染内容输出 |
| xml | XML数据输出 |
| app\_path | 当前应用目录 |
| base\_path | 应用基础目录 |
| config\_path | 应用配置目录 |
| public\_path | web根目录 |
| root\_path | 应用根目录 |
| runtime\_path | 应用运行时目录 |

> 可以在应用的公共函数文件中重写上面这些助手函数。



