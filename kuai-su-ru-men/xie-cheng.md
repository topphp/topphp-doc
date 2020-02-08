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

