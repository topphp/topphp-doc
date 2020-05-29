## 数据库驱动

如果你需要自定义数据库驱动，需要自定义实现`Connection`类（或者继承`think\db\Connection`）和`Builder`类（或者继承`think\db\Builder`），对于特殊的驱动，可能还需要实现`Query`类（或者继承`think\db\Query`）。

> 具体数据库驱动的实现，要根据你的自定义`Connection`类来决定。可以参考官方的`oracle`驱动和`mongo`驱动的实现。

一旦自定义了数据库驱动，例如你自定义实现了`think\mongo\Connection`你需要在数据库配置文件中配置：

```php
'type'  =>   'think\mongo\Connection',
'query' =>   'think\mongo\Query',
```

  


