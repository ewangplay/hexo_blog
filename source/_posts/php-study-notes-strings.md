title: PHP学习笔记——字符串操作
date: 2016-09-14 20:30:55
tags: php
---

PHP作为一个方便使用的脚本语言，对字符串的处理能力也是其突出的一个优势，我们今天就大概浏览一下在PHP中，有那些主要的针对字符串的操作。

## 格式化字符串

### 去除字符串两侧的特殊字符

* trim: 去除字符串开头和结尾的特殊字符

* ltrim: 去除字符串开头的特殊字符

* rtrim: 去除字符串结尾的特殊字符

这里的特殊字符默认包括：换行符(\n)、回车符(\r)、水平制表符(\t)、垂直制表符(\x0B)、字符串结束符(\0)、和空格字符。如果你希望去除这些默认字符之外的一些字符，可以通过这些函数的第二个参数来指定要去除的字符列表。

### 替换回车换行符为HTML标签

* nl2br: 会把字符串中的回车符(\n)、换行符(\r)、回车换行符(\r\n)、换行回车符(\n\r)替换成`<br />`或者`<br>`

### 格式化输出字符串

* echo: 在标准输出上输出一个或多个字符串

* print: 在标准输出上输出一个字符串

* printf: 在标准输出上格式化输出一个字符串

* sprintf: 格式化一个字符串并返回

如果使用过C语言，就很容易理解这些函数的用法，因为它们的含义和使用方法跟C语言中对应的格式化字符串函数基本一样。

### 改变字符串的大小写

* strtoupper: 把字符串中的字符全部转换成大写

* strtolower: 把字符串中字符全部转换成小写

* ucfirst: 把字符串的第一个字符转换成大写

* ucwords: 把字符串中每一个单词的第一个字符转换成大写

### 跟存储字符串相关的格式化函数

有一些字符在字符串中是有效的，但是当存储到数据库中时会产生问题，比如这些字符：单引号(`'`)、双引号(`"`)、反斜杠(`\`)、NULL字符(`NUL`)。
数据库系统会把这些字符特殊的控制字符进行解析，所以必须提供一种机制让数据库系统把这些字符当成普通字符而不进行解析，方法就是在这些字符前面添加一个反斜杠(`\`):

>`'` => `\'`
`"` => `\"`
`\` => `\\`
`NUL` => `\NUL`

PHP提供了两个对应的格式化函数来处理这种情况：

* addslashes: 对字符串中的特殊控制字符添加反斜杠

* stripslashes: 去除字符串中特殊控制字符前面的反斜杠

这是手动的方式，PHP可以通过配置m`agic_quotes_gpc`选项开启自动格式化模式，如果该选项开启的话，从`GET`、`POST`和`COOKIE`这三个来源提交的变量字符串会自动对字符串中的特殊控制字符添加反斜杠。PHP提供了一个函数让你来判断是否开启了自动模式：`get_magic_quotes_gpc()`，这样我们在程序中就可以根据这个判断来决定如何进行处理了。

下面是是一个例子：

```
<?php
// If magic quotes are enabled
echo $_POST['lastname'];             // O\'reilly
echo addslashes($_POST['lastname']); // O\\\'reilly

// Usage across all PHP versions
if (get_magic_quotes_gpc()) {
    $lastname = stripslashes($_POST['lastname']);
}
else {
    $lastname = $_POST['lastname'];
}

// If using MySQL
$lastname = mysql_real_escape_string($lastname);

echo $lastname; // O\'reilly
$sql = "INSERT INTO lastnames (lastname) VALUES ('$lastname')";
?>
```

## 拼装和分解字符串

* explode：按照特定的分隔符对字符串进行分割，分割的元素存储在数组中返回。还可以使用额外的参数`limit`限制分割元素的数量。

该函数的原型如下：
`array explode(string separator, string input [, int limit]);`

我们看一个例子，这个例子把email地址中的域名部分分离出来，然后根据域名来决定把反馈邮件发送到哪里。
```
$email_array = explode(‘@’, $email);
if (strtolower($email_array[1]) == “bigcustomer.com”) { 
    $toaddress = “bob@example.com”;
} else {
    $toaddress = “feedback@example.com”;
}
```

* implode：该函数达到的效果跟`explode`相反，把一个数组元素通过特定的分隔符拼接起来。

该函数的原型如下：
`string implode ( string $glue , array $pieces )`
`string implode ( array $pieces )`

参数`$glue`用来指定拼接时的分隔符，如果没有指定，默认为空格。

* join：是`implode`函数的别名。

* strtok：该函数也是用来分割字符串，与`explode`函数不同的时，这个函数每次调用只分割一个元素，所以需要多次循环调用才能把一个字符串分割完毕。

原型如下：
`string strtok ( string $str , string $token )`
`string strtok ( string $token )`

第一次调用时，传入要分割的字符串`$str`，返回一个根据`$token`分割的元素，接着的调用就不需要传入原始字符串了，只需要按照第二种原型方式调用，就会依次获取后面的分割元素。如果希望开始一个新的字符串分割，那么重新调用第一种原型方式传入新的字符串即可。

下面看一个使用的例子：
```
<?php
$string = "This is\tan example\nstring";
$tok = strtok($string, " \n\t");

while ($tok !== false) {
    echo "$tok\n";
    $tok = strtok(" \n\t");
}
?>
```

* substr：该函数用来从一个字符串中精确的分离出来一个子字符串。

原型如下：
`string substr ( string $string , int $start [, int $length ] )`

## 比较字符串

## 匹配和替换字符串

