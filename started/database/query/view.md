## 视图查询

视图查询可以实现不依赖数据库视图的多表查询，并不需要数据库支持视图，是JOIN方法的推荐替代方法，例如：

```php
Db::view('User', 'id,name')
    ->view('Profile', 'truename,phone,email', 'Profile.user_id=User.id')
    ->view('Score', 'score', 'Score.user_id=Profile.id')
    ->where('score', '>', 80)
    ->select();
```

生成的SQL语句类似于：

```php
SELECT User.id,User.name,Profile.truename,Profile.phone,Profile.email,Score.score FROM think_user User INNER JOIN think_profile Profile ON Profile.user_id=User.id INNER JOIN think_socre Score ON Score.user_id=Profile.id WHERE Score.score > 80
```

> 注意，视图查询无需调用`table`和`join`方法，并且在调用`where`和`order`方法的时候只需要使用字段名而不需要加表名。

默认使用`INNER join`查询，如果需要更改，可以使用：

```php
Db::view('User', 'id,name')
    ->view('Profile', 'truename,phone,email', 'Profile.user_id=User.id', 'LEFT')
    ->view('Score', 'score', 'Score.user_id=Profile.id', 'RIGHT')
    ->where('score', '>', 80)
    ->select();
```

生成的SQL语句类似于：

```php
SELECT User.id,User.name,Profile.truename,Profile.phone,Profile.email,Score.score FROM think_user User LEFT JOIN think_profile Profile ON Profile.user_id=User.id RIGHT JOIN think_socre Score ON Score.user_id=Profile.id WHERE Score.score > 80
```

可以使用别名：

```php
Db::view('User', ['id' => 'uid', 'name' => 'account'])
    ->view('Profile', 'truename,phone,email', 'Profile.user_id=User.id')
    ->view('Score', 'score', 'Score.user_id=Profile.id')
    ->where('score', '>', 80)
    ->select();
```

生成的SQL语句变成：

```php
SELECT User.id AS uid,User.name AS account,Profile.truename,Profile.phone,Profile.email,Score.score FROM think_user User INNER JOIN think_profile Profile ON Profile.user_id=User.id INNER JOIN think_socre Score ON Score.user_id=Profile.id WHERE Score.score > 80
```

可以使用数组的方式定义表名以及别名，例如：

```php
Db::view(['think_user' => 'member'], ['id' => 'uid', 'name' => 'account'])
    ->view('Profile', 'truename,phone,email', 'Profile.user_id=member.id')
    ->view('Score', 'score', 'Score.user_id=Profile.id')
    ->where('score', '>', 80)
    ->select();
```

生成的SQL语句变成：

```php
SELECT member.id AS uid,member.name AS account,Profile.truename,Profile.phone,Profile.email,Score.score FROM think_user member INNER JOIN think_profile Profile ON Profile.user_id=member.id INNER JOIN think_socre Score ON Score.user_id=Profile.id WHERE Score.score > 80
```



