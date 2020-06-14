## 多态一对多关联

多态关联允许一个模型在单个关联定义方法中从属一个以上其它模型，例如用户可以评论书和文章，但评论表通常都是同一个数据表的设计。多态一对多关联关系，就是为了满足类似的使用场景而设计。

下面是关联表的数据表结构：

```php
article
    id - integer
    title - string
    content - text

book
    id - integer
    title - string

comment
    id - integer
    content - text
    commentable_id - integer
    commentable_type - string

```

有两个需要注意的字段是 `comment` 表中的 `commentable_id` 和 `commentable_type`我们称之为多态字段。其中， `commentable_id` 用于存放书或者文章的 id（主键） ，而 `commentable_type` 用于存放所属模型的类型。通常的设计是多态字段有一个公共的前缀（例如这里用的`commentable`），当然，也支持设置完全不同的字段名（例如使用`data_id`和`type`）。

### 多态关联定义

接着，让我们来查看创建这种关联所需的模型定义：

文章模型：

```php
<?php
namespace app\model;

use think\Model;

class Article extends Model
{
    /**
     * 获取所有针对文章的评论。
     */
    public function comments()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}
```

`morphMany`方法的参数如下：

> ### morphMany\('关联模型','多态字段','多态类型'\);

**关联模型**（必须）：关联的模型类名

**多态字段**（可选）：支持两种方式定义 如果是字符串表示多态字段的前缀，多态字段使用`多态前缀_type`和`多态前缀_id`，如果是数组，表示使用\['多态类型字段名','多态ID字段名'\]，默认为当前的关联方法名作为字段前缀。

**多态类型**（可选）：当前模型对应的多态类型，默认为当前模型名，可以使用模型名（如`Article`）或者完整的命名空间模型名（如`app\index\model\Article`）。

书籍模型：

```php
<?php
namespace app\model;

use think\Model;

class Book extends Model
{
    /**
     * 获取所有针对书籍的评论。
     */
    public function comments()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}
```

书籍模型的设置方法同文章模型一致，区别在于多态类型不同，但由于多态类型默认会取当前模型名，因此不需要单独设置。

下面是评论模型的关联定义：

```php
<?php
namespace app\model;

use think\Model;

class Comment extends Model
{
    /**
     * 获取评论对应的多态模型。
     */
    public function commentable()
    {
        return $this->morphTo();
    }
}
```

`morphTo`方法的参数如下：

> ### morphTo\('多态字段',\['多态类型别名'\]\);

**多态字段**（可选）：支持两种方式定义 如果是字符串表示多态字段的前缀，多态字段使用`多态前缀_type`和`多态前缀_id`，如果是数组，表示使用\['多态类型字段名','多态ID字段名'\]，默认为当前的关联方法名作为字段前缀  
**多态类型别名**（可选）：数组方式定义

### 获取多态关联

一旦你的数据表及模型被定义，则可以通过模型来访问关联。例如，若要访问某篇文章的所有评论，则可以简单的使用 `comments` 动态属性：

```php
$article = Article::find(1);

foreach ($article->comments as $comment) {
    dump($comment);
}
```

你也可以从多态模型的多态关联中，通过访问调用 `morphTo` 的方法名称来获取拥有者，也就是此例子中 `Comment` 模型的 `commentable` 方法。所以，我们可以使用动态属性来访问这个方法：

```php
$comment = Comment::find(1);
$commentable = $comment->commentable;
```

`Comment` 模型的 `commentable` 关联会返回 `Article` 或 `Book` 模型的对象实例，这取决于评论所属模型的类型。

### 自定义多态关联的类型字段

默认情况下，ThinkPHP 会使用模型名作为多态表的类型区分，例如，`Comment`属于 `Article` 或者 `Book` , `commentable_type` 的默认值可以分别是 `Article` 或者 `Book` 。我们可以通过定义多态的时候传入参数来对数据库进行解耦。

```php
    public function commentable()
    {
        return $this->morphTo('commentable',[
        	'book'	=>	'app\index\model\Book',
            'post'	=>	'app\admin\model\Article',
        ]);
    }
```

## 多态一对一关联

多态一对一相比多态一对多关联的区别是动态的一对一关联，举个例子说有一个个人和团队表，而无论个人还是团队都有一个头像需要保存但都会对应同一个头像表

```php
member
    id - integer
    name - string
    
team
    id - integer
    name - string
    
avatar
    id - integer
    avatar - string
    imageable_id - integer
    imageable_type - string 

```

会员模型：

```php
<?php
namespace app\model;

use think\Model;

class Member extends Model
{
    /**
     * 获取用户的头像
     */
    public function avatar()
    {
        return $this->morphOne(Avatar::class, 'imageable');
    }
}
```

团队模型：

```php
<?php
namespace app\model;

use think\Model;

class Team extends Model
{
    /**
     * 获取团队的头像
     */
    public function avatar()
    {
        return $this->morphOne(Avatar::class, 'imageable');
    }
}
```

`morphOne`方法的参数如下：

> ### morphOne\('关联模型','多态字段','多态类型'\);

**关联模型**（必须）：关联的模型类名。

**多态字段**（可选）：支持两种方式定义 如果是字符串表示多态字段的前缀，多态字段使用`多态前缀_type`和`多态前缀_id`，如果是数组，表示使用\['多态类型字段名','多态ID字段名'\]，默认为当前的关联方法名作为字段前缀。

**多态类型**（可选）：当前模型对应的多态类型，默认为当前模型名，可以使用模型名（如`Member`）或者完整的命名空间模型名（如`app\index\model\Member`）。

下面是头像模型的关联定义：

```php
<?php
namespace app\model;

use think\Model;

class Avatar extends Model
{
    /**
     * 获取头像对应的多态模型。
     */
    public function imageable()
    {
        return $this->morphTo();
    }
}
```

理解了多态一对多关联后，多态一对一关联其实就很容易理解了，区别就是当前模型和动态关联的模型之间的关联属于一对一关系。

