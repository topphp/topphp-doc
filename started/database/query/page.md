## 分页实现

`ThinkPHP`内置了分页实现，要给数据添加分页输出功能变得非常简单，可以直接在`Db`类查询的时候调用`paginate`方法：

```php
// 查询状态为1的用户数据 并且每页显示10条数据
$list = Db::name('user')->where('status',1)->order('id', 'desc')->paginate(10);

// 渲染模板输出
return view('index', ['list' => $list]);
```

模板文件中分页输出代码如下：

```php
<div>
<ul>
{volist name='list' id='user'}
    <li> {$user.nickname}</li>
{/volist}
</ul>
</div>
{$list|raw}

```

也可以单独赋值分页输出的模板变量

```php
// 查询状态为1的用户数据 并且每页显示10条数据
$list = Db::name('user')->where('status',1)->order('id', 'desc')->paginate(10);

// 获取分页显示
$page = $list->render();

return view('index', ['list' => $list, 'page' => $page]);
```

模板文件中分页输出代码如下：

```php
<div>
<ul>
{volist name='list' id='user'}
    <li> {$user.nickname}</li>
{/volist}
</ul>
</div>
{$page|raw}

```

默认情况下，生成的分页输出是完整分页功能，带总分页数据和上下页码，分页样式只需要通过样式修改即可，完整分页默认生成的分页输出代码为：

```php
<ul class="pagination">
<li><a href="?page=1">&laquo;</a></li>
<li><a href="?page=1">1</a></li>
<li class="active"><span>2</span></li>
<li class="disabled"><span>&raquo;</span></li>
</ul>
```

如果你需要单独获取总的数据，可以使用

```php
// 查询状态为1的用户数据 并且每页显示10条数据
$list = Db::name('user')->where('status',1)->order('id' ,'desc')->paginate(10);
// 获取总记录数
$count = $list->total();
return view('index', ['list' => $list, 'count' => $count]);
```

### 传入总记录数

支持传入总记录数而不会自动进行总数计算，例如：

```php
// 查询状态为1的用户数据 并且每页显示10条数据 总记录数为1000
$list = Db::name('user')->where('status',1)->paginate(10,1000);
// 获取分页显示
$page = $list->render();

return view('index', ['list' => $list, 'page' => $page]);
```

> 对于`UNION`查询以及一些特殊的复杂查询，推荐使用这种方式首先单独查询总记录数，然后再传入分页方法

### 分页后数据处理

支持分页类后数据直接`each`遍历处理，方便修改分页后的数据，而不是只能通过模型的获取器来补充字段。

```php
$list = Db::name('user')->where('status',1)->order('id', 'desc')->paginate()->each(function($item, $key){
    $item['nickname'] = 'think';
    return $item;
});
```

如果是模型类操作分页数据的话，`each`方法的闭包函数中不需要使用返回值，例如：

```php
$list = User::where('status',1)->order('id', 'desc')->paginate()->each(function($item, $key){
    $item->nickname = 'think';
});
```

## 简洁分页

如果你仅仅需要输出一个 仅仅只有上下页的分页输出，可以使用下面的简洁分页代码：

```php
// 查询状态为1的用户数据 并且每页显示10条数据
$list = Db::name('user')->where('status',1)->order('id', 'desc')->paginate(10, true);

// 渲染模板输出
return view('index', ['list' => $list]);
```

简洁分页模式的输出代码为：

```php
<ul class="pager">
<li><a href="?page=1">&laquo;</a></li>
<li class="disabled"><span>&raquo;</span></li>
</ul>
```

> 由于简洁分页模式不需要查询总数据数，因此可以提高查询性能。

## 分页参数

主要的分页参数如下：

| 参数 | 描述 |
| :--- | :--- |
| list\_rows | 每页数量 |
| page | 当前页 |
| path | url路径 |
| query | url额外参数 |
| fragment | url锚点 |
| var\_page | 分页变量 |

分页参数的设置可以在调用分页方法的时候传入，例如：

```php
$list = Db::name('user')->where('status',1)->paginate([
    'list_rows'=> 20,
    'var_page' => 'page',
]);
```

> 如果需要在分页的时候传入查询条件，可以使用`query`参数拼接额外的查询参数

## 大数据分页

对于大量数据的分页查询，系统提供了一个高性能的`paginateX`分页查询方法，用法和`paginate`分页查询存在一定区别。如果你要分页查询的数据量在百万级以上，使用`paginateX`方法会有明显的提升，尤其是在分页数较大的情况下。并且由于针对大数据量而设计，该分页查询只能采用简洁分页模式，所以没有总数。

> 分页查询的排序字段一定要使用索引字段，并且是连续的整型，否则会有数据遗漏。

主要场景是针对主键进行分页查询，默认使用主键倒序查询分页数据。

```php
$list = Db::name('user')->where('status',1)->paginateX(20);
```

也可以在查询的时候可以指定主键和排序

```php
$list = Db::name('user')->where('status',1)->paginateX(20, 'id', 'desc');
```

查询方法会执行两次查询，第一次查询用于查找满足当前查询条件的最大或者最小值，然后配合主键查询条件来进行分页数据查询。

## 自定义分页类

如果你需要自定义分页，可以扩展一个分页驱动。

然后在`provider.php`定义文件中重新绑定

```php
return [
    'think\Paginator'    =>    'app\common\Bootstrap'
];
```



