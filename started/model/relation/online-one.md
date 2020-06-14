## 远程一对一

远程一对一关联用于定义有跨表的一对一关系，例如：

* 每个用户有一个档案
* 每个档案有一个档案卡
* 用户和档案卡之间并无关联

## 关联定义

就可以直接通过远程一对一关联获取每个用户的档案卡，`User`模型定义如下：

```php
<?php
namespace app\model;

use think\Model;

class User extends Model 
{
    public function card()
    {
        return $this->hasOneThrough(Card::class,Profile::class);
    }
}
```

远程一对一关联，需要同时存在`Card`和`Profile`模型。

`hasOneThrough`方法的参数如下：

> ### hasOneThrough\('关联模型', '中间模型', '外键', '中间表关联键','当前模型主键','中间模型主键'\);

* **关联模型**（必须）：关联模型类名
* **中间模型**（必须）：中间模型类名
* **外键**：默认的外键名规则是当前模型名+`_id`
* **中间表关联键**：默认的中间表关联键名的规则是中间模型名+`_id`
* **当前模型主键**：一般会自动获取也可以指定传入
* **中间模型主键**：一般会自动获取也可以指定传入

## 关联查询

我们可以通过下面的方式获取关联数据

```php
$user = User::find(1);
// 获取用户的档案卡
dump($user->card);
```

  


