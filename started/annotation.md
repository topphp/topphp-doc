## 概念

**什么是注解，什么是注释？**

在解释注解之前我们需要先定义一下 `注解` 与 `注释` 的区别：

* 注释：给程序员看，帮助理解代码，对代码起到解释、说明的作用。
* 注解：给应用程序看，用于元数据的定义，单独使用时没有任何作用，需配合应用程序对其元数据进行利用才有作用。

**IDE 注解插件**

因为 `PHP` 并不是原生支持 `注解`，所以 `IDE` 不会默认增加注解支持。但我们可以添加第三方插件，来让 `IDE` 支持 `注解`。

**PhpStorm**

我们到 `Plugins` 中搜索 `PHP Annotations`，就可以找到对应的组件 <a href="https://github.com/Haehnchen/idea-php-annotation-plugin">PHP Annotations</a>。然后安装组件，重启 `PhpStorm`，就可以愉快的使用注解功能了，主要提供了为注解类增加自动跳转和代码提醒支持，使用注解时自动引用注解对应的命名空间等非常便捷有用的功能。

**安装**

```
composer require topthink/think-annotation
```

>TopPHP骨架默认已集成ThinkPHP注解组件，注意，注解功能（`注解路由`、`注解中间件`、`注解验证器`）都是基于ThinkPHP的注解路由实现的，所以如果需要使用`注解中间件`、`注解验证器`，请务必使用`注解路由`定义请求。