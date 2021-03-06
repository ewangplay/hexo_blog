title: PHP学习笔记——变量类型
date: 2016-08-29 9:50:55
tags: [php]
categories: 编程技术
---

## 变量类型

php中的变量类型是由存储于其中的数据类型决定的。

php支持如下的数据类型：
* Integer： 整数类型
* Float(or double): 浮点数类型
* String：字符串类型
* Boolean： 布尔类型，true or false
* Array：数组类型
* Object：对象类型，用来存储类的实例

还有两种特殊的数据类型：
* NULL：变量没有被赋值，或者已经被重置（unset），或者直接赋值为NULL，那么这个变量的类型就是NULL
* Resource： 资源类型，通常描述一些外部资源，比如数据库连接。你不能直接操作这些变量，但通常需要再把它们传入后续的函数进行操作

php被称为弱类型，或者是动态类型语言，就是因为一个变量的类型由赋给它的值所决定，并不是固定不变的。而对于强类型，或者是编译类型语言，一个变量只能表示一种数据类型，而且在使用之前要先定义出来，比如经典的C语言就是这样。

比如，我们在php脚本中使用了一个变量$dogtutu，并且赋初值如下：

```
$dogtutu = 3;
```

因为赋了3给变量，所以这时变量$dogtutu的类型就是Integer类型。随后，我们在脚本中又使用了如下的赋值语句：

```
$dogtutu = "hello";
```

这时候赋了一个字符串给变量$dogtutu，所以这时变量$dogtutu的类型就是String类型。这就是php变量的灵活性。

## 类型转换

php中的类型转换，跟C语言工作模式完全一致，都是直接在变量或数值前面使用`(type)`进行转换。如下所示：

```
$cat = 5;
$dog = (float)$cat;
```

其中变量$cat就是Integer类型，而$dog就是float类型。

## 可变变量

php中提供了一种特殊的变量：可变变量(the variable variable)，可变变量允许你动态的改变变量的名字。

通常，我们定义个变量，名字都是固定的，比如$tireqty，但是php中允许使用另外一个变量作为名字来定义变量。

比如，我们定义了一个变量$varname:

```
$varname = 'dogtutu';
```

那么$$varname就是所谓的可变变量，它使用了变量$varname的值作为了自己的变量名，所以它等价于$dogtutu。比如这时我们执行了下面的语句：

```
$$varname = 5;
echo $dogtutu;
```

输出的值就是5，是不是很神奇。

但是我们可能会疑惑，这样做的目的是什么？这个特性有什么用？我能想到的一个场景就是，当你有很多的变量需要统一处理，你一个一个处理太麻烦，这时就可以使用一个循环，用可变变量遍历所有的这些变量，做一个统一的处理，是不是就方便了很多呢。

如你所见，php真是是一个很灵活的语言，它不但允许你动态改变变量的类型，而且允许你动态改变变量的名字，真的是自由无极限啊。