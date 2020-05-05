### 工具方法组件

#### `TopUtilStr` 字符串操作类

* 检查字符串中是否包含某些字符串
`TopUtilStr::contains($haystack, $needles)`

* 检查字符串是否以某些字符串结尾
`TopUtilStr::endsWith(string $haystack, $needles)`

* 检查字符串是否以某些字符串开头
`TopUtilStr::startsWith(string $haystack, $needles)`

* 获取指定长度的随机字母数字组合的字符串
`TopUtilStr::random(int $length, int $type, string $addChars)`

* 字符串转小写
`TopUtilStr::lower(string $value)`

* 字符串转大写
`TopUtilStr::upper(string $value)`

* 获取字符串的长度
`TopUtilStr::length(string $value)`

* 截取字符串
`TopUtilStr::substr(string $string, int $start, int $length = null)`

* 驼峰转下划线
`TopUtilStr::snake(string $value, string $delimiter = '_')`

* 下划线转驼峰(首字母小写)
`TopUtilStr::camel(string $value)`

* 下划线转驼峰(首字母大写)
`TopUtilStr::studly(string $value)`

* 转为首字母大写的标题格式
`TopUtilStr::title(string $value)`


