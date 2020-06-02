## topphp-generate 数据库实体类生成组件

该组件可快速生成 `ThinkPHP` 的模型类，命名统一为`xxxDao`，继承自 `app\model\entity\XXX.php`数据模型实体类，自动创建并`use BaseModel;`Trait类。

`BaseModel.php`为基础模型公共类，用于编写常用的增删改查公共方法，或者有关项目的模型公共方法。仅在不存在`BaseModel.php`文件时自动创建，以后再运行命令不会对此文件进行改变或删除，所以用户可自己更改里面的代码。

### 安装

```bash
composer require topphp/topphp-generate
```

### 使用方法

需要先配置数据库信息 `config/database.php` 后进行命令行生成：

```shell
php think gen:db
```

需要使用`TopPHP`提供的`BaseModel`类的话执行：

```shell
php think gen:db -b
```

> 程序会在 `app/model/entity`目录下创建实体类，文件名以不包含表前缀的数据表名命名，如：`topphp_user`表，即为`User.php`。

**效果图**

![](/assets/db.png)![](/assets/gendb1.png)![](/assets/gendb2.png)

> 同时会在所有的应用目录创建对应的`xxxDao.php`的模型文件。`gen:db`命令会根据你是否创建了对应的应用模块（如`admin`）下的`model`的文件夹来判断是否自动创建对应的`model`文件，如果存在`model`文件夹，就会自动创建。

上图的`php think gen:db -b`命令中`-b`参数表示是否使用`TopPHP`为你提供的`BaseModel`基础模型公共方法类，内部包含常用的对于数据表的`增删改查`操作快捷方法，以及一些你可能用到的常用公共方法。

如果你不需要这些快捷方法或者是你需要自己实现公共方法，也可以去掉`-b`参数，默认直接创建一个空的`BaseModel`类。

你还可以使用`TopPHP`为你提供的`BaseModel`基础模型公共方法类的同时，向类中增加你特有的方法，每次执行`php think gen:db`时并不会覆盖掉你的方法。

### 注意事项

* 自动生成：在topphp骨架里，多应用情况下，gen:db组件会根据你是否创建了对应的应用模块（如admin）下的model的文件夹来判断是否自动创建对应的model文件
* 创建规范：应用模块（如admin）下的model文件统一会继承`app/model/entity`下的模型实体类,并全部以Dao.php结尾（此Dao层主要用于编写数据库业务）
* 添加新的表：如果开发过程中需要添加新的数据表，可以执行 php think gen:db 进行自动更新模型实体类与模型Dao
* `BaseModel`类：需要使用`TopPHP`提供的`BaseModel`类时，请先备份并删除`app/model`下原有的`BaseModel`，然后执行`php think gen:db -b`命令。
* 特别说明：
      a、每次执行 php think gen:db 创建的模型实体类都会重置一遍，所以请不要直接编辑操作`app/model/entity`下的模型实体类。
      b、每次执行 php think gen:db 对于已存在的模型Dao不会清空代码，对于不存在的模型Dao会自动创建（例如中途添加新的数据表场景）。
      c、每次执行 php think gen:db 以后使用传统环境（apache或nginx）部署在Linux系统下的注意执行以后的文件夹权限，如果不一致，需统一修改一次文件夹权限（如：chown -R www:www www.domain.com/）



