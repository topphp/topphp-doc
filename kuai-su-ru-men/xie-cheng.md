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

>在 `topphp` 里实现协程的上下文管理将非常简单，基于 Topphp\TopphpSwoole\coroutine\Context 类的 set($key, $item, int $cid = null)、get($key, int $cid = null, $default = null)、has($key, int $cid = null)、delete(string $id, \Closure $closure) 静态方法即可完成上下文数据的管理，通过这些方法设置和获取的值，都仅限于当前的协程，在协程结束时，对应的上下文也会自动跟随释放掉，无需手动管理，无需担忧内存泄漏的风险。

#### Context::set()
>通过调用set方法存储y一个值到当前线协程上下文中,示例如下:
```php
Context::set('info', [1, 2, 3], Coroutine::getuid());
```
#### Context::get()
>通过调用`get`方法可从当前协程的上下文中取出一个以 `$key` 为 key 储存的值，如不存在则返回 $default ，示例如下：
```php
$info = Context::get('info', Coroutine::getuid(), [1,2,3]);  // get context of this coroutine
var_dump($info); 
// [1,2,3]
```

#### Context::has()
>通过调用has方法可判断当前协程的上下文中是否存在以 `$key` 为 key 储存的值，如存在则返回 true，不存在则返回 false，示例如下：
```php
$info = Context::has('info');
// true
```

#### Context::delete()
>通过调用delete方法可删除当前协程的上下文中以 `$key` 为 key 储存的值，示例如下：
```php
$info = Context::delete('info',Coroutine::getuid());
// true
```

#### Context::override()
> 当我们需要做一些复杂的上下文处理，比如先判断一个 key 是否存在，如果存在则取出 value 来再对 value 进行某些修改，然后再将 value 设置回上下文容器中，此时会有比较繁杂的判断条件，可直接通过调用 override 方法来实现这个逻辑，
示例如下：
```php
$info = Context::delete('info',Coroutine::getuid());
// true
```


















