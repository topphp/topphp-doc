## 目录结构

> `TopPHP` 默认采用 `ThinkPHP 6` 的多应用模式，如果开发者不需要某些默认应用，可以直接自行删除对应的应用目录，并不影响整个骨架的运行。

```
www  WEB部署目录（或者子目录）
├─app           应用目录
│  ├─admin                  后台管理应用
│  │  ├─common.php          函数文件
│  │  ├─controller          控制器目录 -- Tips：数据入参的基本验证处理与发送客户端响应，业务逻辑应尽量移步到 service 下
│  │  │  ├─Base.php         控制器基础抽象类（所有新建控制器都需要继承此类）
│  │  │  ├─Index.php        Index控制器
│  │  │  └─ ...             更多控制器
│  │  │
│  │  ├─model               模型目录 -- Tips：一般应仅用于对数据库的操作，业务逻辑应尽量移步到 service 下
│  │  │  ├─DemoDao.php      示例模型（1、所有新建模型需继承 app\model\entity 下对应的模型实体类）
│  │  │  └─ ...             更多模型（2、所有新建模型都应以 xxxDao.php 命名，推荐使用TopPHP的 topphp/topphp-generate 组件自动生成）
│  │  │
│  │  ├─view                视图目录（如果需要可自行创建，骨架已自动加载了 topthink/think-view 视图组件）
│  │  ├─config              配置目录
│  │  │  ├─middleware.php   中间件配置（骨架默认配置了全局验证器 Check 中间件和 Auth 权限中间件，权限中间件内容需要开发者自行填充业务）
│  │  │  └─ ...             更多配置
│  │  │
│  │  ├─middleware          admin应用中间件目录
│  │  │  ├─Auth.php         Auth权限中间件（骨架已在控制器基础抽象类 Base.php 配置好了该中间件，只需要开发者自行填充此中间件业务即可实现控制器权限控制）
│  │  │  └─ ...             更多中间件
│  │  │
│  │  ├─route               路由目录
│  │  ├─service             服务目录 -- Tips：用于编写业务逻辑代码，或应用于微服务场景
│  │  │  ├─DemoService.php  示例服务文件（服务文件名应统一以 xxxService.php 命名，且默认不依赖任何模块）
│  │  │  └─ ...             更多服务
│  │  │
│  │  ├─validate            验证器目录 -- Tips：用于配置指定的控制器验证器验证规则，与TopPHP的 Check 全局验证器配合使用
│  │  │  ├─IndexCheck.php   Index控制器验证类（验证器名称必须统一以 xxxCheck.php 命名，Check 全局验证器会自动触发验证，并以json code形式响应）
│  │  │  └─ ...             更多验证器
│  │  │
│  │  └─ ...                更多类库目录
│  │
│  ├─api                    Api应用目录 -- Tips：默认提供的是多版本的接口应用，开发者如果没有多版本接口需求，可以删除此应用目录
│  │  ├─common.php          函数文件
│  │  ├─controller          控制器目录
│  │  │  ├─v1               v1版本目录
│  │  │  │  ├─Base.php      控制器基础抽象类
│  │  │  │  ├─Index.php     Index控制器
│  │  │  │  └─ ...          更多控制器
│  │  │  │
│  │  │  ├─v2               v2版本目录
│  │  │  │  ├─Base.php      控制器基础抽象类
│  │  │  │  ├─Index.php     Index控制器
│  │  │  │  └─ ...          更多控制器
│  │  │  │
│  │  │  └─ ...             更多版本
│  │  │
│  │  ├─model               模型目录
│  │  ├─view                视图目录
│  │  ├─config              配置目录
│  │  ├─route               路由目录
│  │  │  └─route.php        版本路由配置（如果需要添加更多版本，开发者只需要效仿此文件的配置，新增版本路由即可）
│  │  │
│  │  ├─service             服务目录
│  │  │  ├─DemoService.php  示例服务文件
│  │  │  └─ ...             更多服务
│  │  │
│  │  ├─validate            验证器目录
│  │  │  ├─v1               v1版本验证器目录
│  │  │  │  ├─IndexCheck.php
│  │  │  │  └─ ...     
│  │  │  ├─v2               v2版本验证器目录
│  │  │  │  ├─IndexCheck.php
│  │  │  │  └─ ...  
│  │  │  └─ ...             更多版本验证器
│  │  │
│  │  └─ ...                更多类库目录
│  │
│  ├─behavior               应用事件行为目录（使用 ThinkPHP 6 的事件系统）
│  │  ├─listener            事件监听目录
│  │  │  ├─Cache.php        缓存事件监听（Redis缓存失败自动切换文件缓存）
│  │  │  └─ ...             更多业务事件监听
│  │  └─ ...                其他行为目录（如事件订阅）
│  │
│  ├─common                 应用公共目录
│  │  ├─enumerate           公共枚举类目录 -- Tips：枚举类应统一使用 xxxEnum.php 命名，以下基础枚举类请勿删除
│  │  │  │
│  │  │  ├─CheckConfigEnum.php  验证器配置枚举（如：配置验证器白名单）
│  │  │  ├─CommonCodeEnum.php   公共 Code码枚举
│  │  │  ├─HttpStatusEnum.php   Http 状态码枚举
│  │  │  ├─MethodEnum.php       RestFul请求方法枚举
│  │  │  ├─PaginateEnum.php     分页配置枚举
│  │  │  └─ ...                 更多枚举
│  │  │
│  │  └─ ...                更多公共类库目录
│  │
│  ├─index                  index应用目录 -- Tips：结构与admin应用基本一致
│  │  ├─common.php          函数文件
│  │  ├─controller          控制器目录
│  │  ├─model               模型目录
│  │  ├─view                视图目录
│  │  ├─config              配置目录
│  │  ├─middleware          index应用中间件目录
│  │  ├─route               路由目录
│  │  ├─service             服务目录
│  │  ├─validate            验证器目录
│  │  └─ ...                更多类库目录
│  │
│  ├─app_name               其他应用目录
│  │  ├─common.php          函数文件
│  │  ├─controller          控制器目录
│  │  ├─model               模型目录
│  │  ├─view                视图目录
│  │  ├─config              配置目录
│  │  ├─middleware          应用中间件目录
│  │  ├─route               路由目录
│  │  ├─service             服务目录
│  │  ├─validate            验证器目录
│  │  └─ ...                更多类库目录
│  │
│  ├─middleware             多应用公共中间件目录
│  │  ├─Check.php           全局验证器中间件
│  │  └─ ...                更多公共中间件
│  │
│  ├─model                  多应用公共模型目录
│  │  ├─entity              数据模型实体类目录（对应数据库每一张表结构，不可填充业务）
│  │  │  ├─Demo.php         示例Demo表结构（各应用需要对此表进行数据操作的话，请继承此类填充业务）
│  │  │  └─ ...             其他数据表实体类
│  │  ├─BaseModel.php       公共模型方法Trait类（可定制）  
│  │  └─ ...                更多公共模型类
│  │
│  ├─common.php             公共函数文件
│  └─event.php              事件定义文件
│
├─config                    全局配置目录
│  ├─app.php                应用配置
│  ├─cache.php              缓存配置
│  ├─console.php            控制台配置
│  ├─cookie.php             Cookie配置
│  ├─database.php           数据库配置
│  ├─filesystem.php         文件磁盘配置
│  ├─lang.php               多语言配置
│  ├─log.php                日志配置
│  ├─middleware.php         中间件配置
│  ├─route.php              URL和路由配置
│  ├─session.php            Session配置
│  ├─trace.php              Trace配置
│  └─view.php               视图配置
│
├─public                    WEB目录（对外访问目录）
│  ├─index.php              入口文件
│  ├─router.php             快速测试文件
│  └─.htaccess              用于apache的重写
│
├─extend                    扩展类库目录
│  ├─lib                    lib目录
│  │  ├─SendMsg.php         请求Code响应类
│  │  ├─TopException.php    Top异常处理类
│  │  ├─TopExceptionTmpl.php Top异常处理模板
│  │  └─ ...                更多基础工具类
│  │
│  └─ ...                   其他扩展目录
│
├─runtime                   应用的运行时目录（可写，可定制）
├─vendor                    Composer类库目录
├─.example.env              环境变量示例文件
├─composer.json             composer 定义文件
├─LICENSE.txt               授权说明文件
├─README.md                 README 文件
├─think                     命令行入口文件
```

在实际的部署中，请确保只有public目录可以对外访问。

> 在`mac`或者`linux`环境下面，注意需要设置`runtime`目录权限为777。

### 默认应用文件

默认安装后，app目录下会包含下面的文件。

```
├─app           应用目录
│  │
│  ├─BaseController.php    默认基础控制器类
│  ├─ExceptionHandle.php   应用异常定义文件
│  ├─common.php            全局公共函数文件
│  ├─middleware.php        全局中间件定义文件
│  ├─provider.php          服务提供定义文件
│  ├─Request.php           应用请求对象
│  └─event.php             全局事件定义文件
```

`BaseController.php`、`Request.php` 和`ExceptionHandle.php`三个文件是系统默认提供的基础文件，位置你可以随意移动，但注意要同步调整类的命名空间。如果你不需要使用`Request.php` 和`ExceptionHandle.php`文件，或者要调整类名，记得必须同步调整`provider.php`文件中的容器对象绑定。

> `provider.php`服务提供定义文件只能全局定义，不支持在应用下单独定义。
>
> `Request.php`TopPHP已做相关兼容性处理，不推荐移动。

### TopPHP编程规范

通过上面的目录结构我们可以看出一个应用大概的编程方式，控制器接收请求，对于请求入参进行验证器验证以及一些基本参数验证，由控制器调用`service`目录下对应的服务类（如登录服务），我们应将大部分复杂的业务的处理都放在服务类进行，如果有对数据库的操作，我们需要在服务类中实例对应的模型，我们在模型中仅编写对数据的操作，然后返回给服务层，由服务层再返回给控制器进行响应，形成一次请求响应的闭环。
