title: PHP学习笔记——访问表单变量
date: 2016-08-28 10:10:55
tags: [php]
categories: 编程技术
---
在使用PHP开发Web应用时，最常见的需求就是使用一个表单（Form）收集用户提交的数据。在PHP中，通过Form表单获取用户提交的数据非常的容易，但是在细节上也存在细微的差别，这一点依赖于你使用的PHP的版本和在php.ini中一些配置。

在PHP脚本中，你可以使用跟表单字段同名的变量来访问每一个表单字段的值，依据你PHP的版本和配置，有三种访问的方式。

假设表单中有一输入框，命名为tireqty，那么对应于这三种方式如下：

## 短样式变量

```
$tireqth    // short style
```

这种方式使用很方便，直接使用跟表单字段同名的变量访问数据，但是需要在php.ini中启用`register_globals`配置，出于安全的原因，这个配置默认是关闭的。启用该选项，会带了很大的安全漏洞，可以直接通过Web页面提交特殊数据来控制服务端PHP脚本的执行流程，所以这种方式是不推荐使用的，这也是该选项之所以默认关闭的原因。

我使用的PHP版本是5.5.36，我看了一下我的php.ini文件，发现没有该配置选项，所以我就Bing了一下，发现了如下信息：

> ** register_globals **
Whether or not to register the EGPCS (Environment, GET, POST, Cookie, Server) variables as global variables.
本特性已自 PHP 5.3.0 起废弃并将自 PHP 5.4.0 起移除。

原来官方在5.4版本中就已经移除了该选项，怪不得我寻她不见呢，看来这种方式虽然很诱人但成昨日黄花了。

## 中样式变量

```
$_POST['tireqty']   // medium style
```

这种方式说长不长、说短不短，符合中庸之道也，而且在安全上也相对有保障，所以是推荐使用的一种方式。在使用上有个下技巧，就是人为的变换成短样式变量，这样方便在脚本中使用，如下：

```
$tireqty = $_POST['tireqty']
```

## 长样式变量

```
$HTTP_POST_VARS['tireqty']  // long style
```

这种方式最为繁琐，但也最为通用——也就是说对老版本的PHP服务器兼容性最好，所以如果你的PHP程序要考虑兼容老版本服务器，可以使用这种方式。但是因为性能的原因，官方已经在新版本的PHP中准备废弃和移除该特性。

该特性通过`register_long_arrays`配置项来控制关闭，默认是开启的。

但我查看了一下我的php.ini配置文件，没有找到该选项。然后我又轻轻的Bing了一下，在PHP的官方文档发现如下信息：

> **register_long_arrays**
Tells PHP whether or not to register the deprecated long $HTTP_*_VARS type predefined variables. When On (default), long predefined PHP variables like $HTTP_GET_VARS will be defined. If you're not using them, it's recommended to turn them off, for performance reasons. Instead, use the superglobal arrays, like $_GET. This directive became available in PHP 5.0.0.
Warning: 本特性已自 PHP 5.3.0 起废弃并将自 PHP 5.4.0 起移除。

看来又不用纠结这个选项了，已经被官方给干掉了。三种方式被废掉了两个，瞬间感觉世界又美好了起来——选择太多也是一种烦恼啊。