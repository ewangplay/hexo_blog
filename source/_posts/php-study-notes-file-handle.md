title: PHP学习笔记——文件操作
date: 2016-09-02 15:00:55
tags: [php]
categories: 编程技术
---

## 打开文件

### fopen

PHP中使用`fopen`函数打开一个文件，原型如下：

`resource fopen ( string $filename , string $mode [, bool $use_include_path = false [, resource $context ]] )`

* `$filename`：要打开的文件路径和名称，支持本地文件和远程文件。如果是远程文件（比如使用ftp、http等协议访问文件），那么要确认配置文件php.ini中`allow_url_fopen`选项开启。

* `$mode`: 打开文件的模式。是只读，只写，还是读写打开。如果写入文件，是覆盖原有的内容，还是在原有内容后面追加。是作为本文文件处理，还是二进制文件处理。

* `$use_include_path`: 该参数是可选的。用来设定要打开的文件是否需要在`include_path`中搜寻，默认为false。如果设定为1或者true，那么第一个参数可以只指定文件名不指定路径，而是从PHP设定的`include_path`路径中搜索。

* `$resource`: 该参数是可选的。如果打开的文件是一个远程文件，有些远程协议需要一些额外的连接参数，就可以在这里指定。

通常，我们就使用两个参数的版本，看下面一个打开文件的例子：

```
@ $fp = fopen(“$DOCUMENT_ROOT/orders/orders.txt”, ‘ab’); 
if (!$fp)
{
    echo "<p><strong>Your order could not be processed at this time. </strong></p>";
    exit; 
}
```

## 写文件

PHP中写入文件有以下几种方式：

### fwrite

`fwrite()`是常规的文件写入函数，原型如下：

`int fwrite ( resource $handle , string $string [, int $length ] )`

* `$handle`: 打开文件的资源句柄，由fopen函数返回。

* `$string`: 要写入文件的字符串内容。

* `$length`: 是可选的参数。如果指定了写入的长度，那么按照指定长度写入内容。

### fputs

该函数是fwrite函数的别名，跟fwrite函数完全一样。

### fputcsv

该函数对数组元素进行格式化处理后再写入文件，原型如下：

`int fputcsv ( resource $handle , array $fields [, string $delimiter = "," [, string $enclosure = '"' [, string $escape_char = "\" ]]] )`

* `$handle`: 打开文件的资源句柄。

* `$fields`: 包含要格式化数据的一维数组。

* `$delimiter`: 可选参数。设置数据字段的间隔符，一个字符。默认为逗号(`,`)。

* `$enclosure`: 可选参数。设置数据字段的包含符，一个字符。默认为双引号(`"`)。

* `$escape_char`: 可选参数，设置数据字段中的转义字符，一个字符。默认为斜杠(`\`)。

下面我们看一个使用`fputcsv()`函数的例子：

```
<?php

$list = array (
    array('aaa', 'bbb', 'ccc', 'dddd'),
    array('123', '456', '789'),
    array('"aaa"', '"bbb"')
);

$fp = fopen('file.csv', 'w');

foreach ($list as $fields) {
    fputcsv($fp, $fields);
}

fclose($fp);
?>
```

上述代码把一个二维数组中的数据分行进行格式化写入文件。

### file_put_contents

这个函数比较牛气，它把打开文件、写入文件、关闭文件这三个步骤合并成了一个步骤。如果文件不存在，会自动创建；如果文件已经存在，设置了`FILE_APPEND`的参数就会追加数据，否则覆盖原文件。

该函数的原型如下：

`int file_put_contents ( string $filename , mixed $data [, int $flags = 0 [, resource $context ]] )`

下面是一个使用该函数例子：

```
<?php
$file = 'people.txt';

$person = "John Smith\n";

file_put_contents($file, $person, FILE_APPEND | LOCK_EX);
?>
```

以追加的方式把内容写入文件，并且写入时独占文件。

## 读文件

读取文件有以下几种方式：

### fread
读取任一长度数据的函数，原型如下：
`string fread ( resource $handle , int $length )`

### fgets
读取一行数据的函数，原型如下：

`string fgets ( resource $handle [, int $length ] )`

如果指定了长度，那么按指定长度读取。如果没有指定长度，那么读取直到换行符或者文件结束。

### fgetss
该函数类似于`fgets`，但不同的是该函数会把读取内容中的NUL字节、HTML标签、PHP标签等统统去掉。

原型如下：

`string fgetss ( resource $handle [, int $length [, string $allowable_tags ]] )`

最后一个可选的参数可以指定不被去掉的例外标签字符。

### fgetcsv

原型如下：
`array fgetcsv ( resource $handle [, int $length = 0 [, string $delimiter = "," [, string $enclosure = '"' [, string $escape = "\" ]]]] )`

该函数类似于`fgets`, 每次读取一行，不同的是该函数会对读取的内容按照csv格式进行解析，然后把解析出来的数据字段生成一个数组返回。

### file_get_contents
该函数用来从文件中读取所有数据内容，而且是官方推荐的一个函数。

原型如下：
`string file_get_contents ( string $filename [, bool $use_include_path = false [, resource $context [, int $offset = 0 [, int $maxlen ]]]] )`


### readfile
该函数也是读取整个文件的内容，跟`file_get_contents`不同的是，它把读取的内容输出到了标准输出流（比如浏览器），而不是返回一个字符串。

原型如下：
`int readfile ( string $filename [, bool $use_include_path = false [, resource $context ]] )`

### fpassthru
该函数类似于`readfile`，也是把读取的内容输出到标准输出上，不同的是它是从文件描述符的当前位置开始读取直到文件结束。

原型如下：
`int fpassthru ( resource $handle )`

### file
该函数也是读取整个文件内容，跟`readfile`不同的是，它不返回字符串内容，而是解析成数组返回。

原型如下：
`array file ( string $filename [, int $flags = 0 [, resource $context ]] )`

### fgetc 
该函数一个一个字符的读取文件，原型如下：
`string fgetc ( resource $handle )`

## 关闭文件

### fclose

关闭文件的函数非常简单，也没啥好交代的，原型如下：

`bool fclose ( resource $handle )`

## 其它一些有用的文件处理函数

### 判断文件是否存在
`bool file_exists ( string $filename )`

### 文件大小
`int filesize ( string $filename )`

### 删除文件
`bool unlink ( string $filename [, resource $context ] )`

### 在文件内导航

* `rewind()`: 把文件位置描述符重置到文件开始，原型：`bool rewind ( resource $handle )`。

* `fseek()`: 设置当前的文件位置描述符到任意位置。原型：`int fseek ( resource $handle , int $offset [, int $whence = SEEK_SET ] )`。

* `ftell()`: 返回文件描述符的当前位置。原型：`int ftell ( resource $handle )`。

## 锁定文件

PHP中操作文件时希望独占，特别是写入数据时，独占文件是有必要的。可以使用`flock`函数来完成，原型如下：

`bool flock ( resource $handle , int $operation [, int &$wouldblock ] )`

主要是第二个参数，相关选项如下：

* LOCK_SH: 获取一个共享锁，读文件时可用
* LOCK_EX: 获取一个只读锁，写文件时可用
* LOCK_UN: 释放一个锁，可以使共享锁或者只读锁
* LOCK_NB: 这个选项可以跟上面三个叠加使用，用来表示在进行上述锁定操作时不阻塞

上面对PHP的文件操作做了一个大概的梳理，可以看到PHP对C语言的借鉴有多么厉害，几乎上就是对C语言中文件操作的Copy，只不过加入了自己的一些更加高级的特性，方便操作。如果之前对C语言有较好的掌握，那么学习起来PHP就省力了很多啊。
