## 一对多关联

### 关联定义

一对多关联的情况也比较常见，使用`hasMany`方法定义，参数包括：

> ### hasMany\('关联模型','外键','主键'\);

除了关联模型外，其它参数都是可选。

* **关联模型**（必须）：关联模型类名
* **外键**：关联模型外键，默认的外键名规则是当前模型名+`_id`
* **主键**：当前模型主键，一般会自动获取也可以指定传入

例如一篇文章可以有多个评论

```php
<?php
namespace app\model;

use think\Model;

class Article extends Model 
{
    public function comments()
    {
        return $this->hasMany(Comment::class);
    }
}
```

同样，也可以定义外键的名称

```php
<?php
namespace app\model;

use think\Model;

class Article extends Model 
{
    public function comments()
    {
        return $this->hasMany(Comment::class,'art_id');
    }
}
```

### 关联查询

我们可以通过下面的方式获取关联数据

```php
$article = Article::find(1);
// 获取文章的所有评论
dump($article->comments);
// 也可以进行条件搜索
dump($article->comments()->where('status',1)->select());
```

### 根据关联条件查询

可以根据关联条件来查询当前模型对象数据，例如：

```php
// 查询评论超过3个的文章
$list = Article::has('comments','>',3)->select();
// 查询评论状态正常的文章
$list = Article::hasWhere('comments',['status'=>1])->select();
```

如果需要更复杂的关联条件查询，可以使用

```php
$where = Comment::where('status',1)->where('content', 'like', '%think%');
$list = Article::hasWhere('comments', $where)->select();
```

### 关联新增

```php
$article = Article::find(1);
// 增加一个关联数据
$article->comments()->save(['content'=>'test']);
// 批量增加关联数据
$article->comments()->saveAll([
    ['content'=>'thinkphp'],
    ['content'=>'onethink'],
]);
```

### 定义相对的关联

要在 Comment 模型定义相对应的关联，可使用`belongsTo`方法：

```php
<?php
name app\model;

use think\Model;

class Comment extends Model 
{
    public function article()
    {
        return $this->belongsTo(Article::class);
    }
}
```

### 关联删除

在删除文章的同时删除下面的评论

```php
$article = Article::with('comments')->find(1);
$article->together(['comments'])->delete();
```



