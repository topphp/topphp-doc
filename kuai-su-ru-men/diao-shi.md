

基于 xdebug2.7(https://github.com/xdebug/xdebug)
### 注意事项
>为了避免 swoole 的检测 xdebug 警告, 扩展注册的名称是 sdebug, 如果想使用 Phpunit CodeCoverage , 需要手动把检测 xdebug 的判断修改成 sdebug

>单步调试: 如果php不是7.3的, 建议使用 sdebug_2_6(https://github.com/mabu233/sdebug/tree/sdebug), sdebug_2_7 可能需要与phpstorm2019搭配使用


### 第一步
```shell

git clone https://github.com/swoole/sdebug.git
```
### 第二步 编译
```shell
cd sdebug
./rebuild.sh
```