## 控制器

按照ThinkPHP的架构设计，所有的URL请求（无论是否采用了路由），最终都会定位到控制器（也许实际的类不一定是控制器类，但也属于广义范畴的控制器）。控制器的层可能有很多，为了便于区分就把通过URL访问的控制器称之为访问控制器（通常意义上我们所说的控制器就是指访问控制器）。

ThinkPHP 的控制器定义比较灵活，可以无需继承任何的基础类，也或者根据业务需求封装自己的基础控制器类。