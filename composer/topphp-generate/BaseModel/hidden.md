## 隐藏字段

有些场景需要对查询出来的数据某个字段隐藏，我们可以在模型中加入隐藏字段的`protected`属性`$hidden`：

```php
namespace app\index\model;

use app\model\entity\User;

class UserDao extends User
{
    protected $hidden = [
        "password",
        "delete_time",
    ];
}
```

这种方式是永久隐藏的方式，如果你仅需要某些特殊情况临时去隐藏字段可以使用`ThinkPHP`模型中的链式操作`hidden()`方法进行隐藏：

```php
$user = User::find(1);
dump($user->hidden(['create_time','update_time'])->toArray());
```

如果你使用的是`TopPHP`提供的`BaseModel`类中的相关查询方法，你可以定义永久隐藏的`$hidden`属性，在需要获取隐藏的字段信息时调用模型中的`getModelData`方法来获取原始的数据库数据。

例如：我们有一个编辑用户信息的接口，接口中包含编辑用户密码的需求，但是查询展示时，我们又不想把原密码字段返回给前端，我们可以在`$hidden`属性中加入`password`永久隐藏它，因为除了这个场景，可能我们其他的查询用户信息的场景都不希望返回这个字段，但是只有这个场景我们还需要获取原始密码，需要验证用户原始密码输入的是否正确，那么可以通过下面的方式来实现：

```php
namespace app\index\model;

use app\model\entity\User;
use lib\SendMsg;

class UserDao extends User
{
    // 用户模型依旧设置"password"隐藏
    protected $hidden = [
        "password",
        "delete_time",
    ];
    
    public function editUser($data = ["id" => 1])
    {
        // data 参数只传主键 id 时默认返回的是查询数据，edit方法的详细说明可以参看【修改和编辑】章节
        $res = $this->edit($data);
        if ($res === false) {
            return SendMsg::arrayAlert(40000, "操作失败");
        }
        dump($res);
        // 此时操作成功返回的 $res 数据是不包含 password 字段的，我们调用 getModelData 方法，即可以获取到全部数据。
        $res = $this->getModelData();
        dump($res);
        return SendMsg::arrayData($res);
    }
}
```

