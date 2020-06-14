## 远程一对多

远程一对多关联用于定义有跨表的一对多关系，例如：

* 每个城市有多个用户
* 每个用户有多个话题
* 城市和话题之间并无关联

## 关联定义

就可以直接通过远程一对多关联获取每个城市的多个话题，`City`模型定义如下：

```php
<?php
namespace app\model;

use think\Model;

class City extends Model 
{
    public function topics()
    {
        return $this->hasManyThrough(Topic::class, User::class);
    }
}
```

> 远程一对多关联，需要同时存在`Topic`和`User`模型，当前模型和中间模型的关联关系可以是一对一或者一对多。

`hasManyThrough`方法的参数如下：

> ### hasManyThrough\('关联模型', '中间模型', '外键', '中间表关联键','当前模型主键','中间模型主键'\);

* **关联模型**（必须）：关联模型类名
* **中间模型**（必须）：中间模型类名
* **外键**：默认的外键名规则是当前模型名+`_id`
* **中间表关联键**：默认的中间表关联键名的规则是中间模型名+`_id`
* **当前模型主键**：一般会自动获取也可以指定传入
* **中间模型主键**：一般会自动获取也可以指定传入

## 关联查询

我们可以通过下面的方式获取关联数据

```php
$city = City::find(1);
// 获取同城的所有话题
dump($city->topics);
// 也可以进行条件搜索
dump($city->topics()->where('topic.status',1)->select());
```

> 条件搜索的时候，需要带上模型名作为前缀

### 根据关联条件查询

如果需要根据关联条件来查询当前模型，可以使用

```php
$list = City::hasWhere('topics', ['status' => 1])->select();
```

更复杂的查询条件可以使用

```php
$where = Topic::where('status', 1)->where('title', 'like', '%think%');
$list = City::hasWhere('topics',$where)->select();
```



