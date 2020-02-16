### 因为关闭了swoole短名称,所以使用协程是要使用类方法来实现

`swoole.use_shortname='Off'`

```php
Coroutine::create(function () {
    $r = new ResetVarDumper();
    $r->handle($this->request, function () {
        var_dump('haha');
    });
})
```

### 协程上下文 Context
由于同一个进程内协程间是内存共享的，但协程的执行/切换是非顺序的，也就意味着我们很难掌控当前的协程是哪一个(事实上可以，但通常没人这么干)，所以我们需要在发生协程切换时能够同时切换对应的上下文。

### 作用
- 协程退出后上下文自动清理 (如无其它协程或全局变量引用)
- 无defer注册和调用的开销 (无需注册清理方法, 无需调用函数清理)
- 无PHP数组实现的上下文的哈希计算开销 (在协程数量巨大时有一定好处)
- Context使用ArrayObject, 满足各种存储需求 (既是对象, 也可以以数组方式操作)

