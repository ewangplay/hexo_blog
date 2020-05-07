title: PHP学习笔记——变量处理函数
date: 2016-08-31 16:15:55
tags: [php]
categories: 编程技术
---

PHP专门提供了一个用于操作和测试变量的函数库。

## 测试和设置变量类型的函数

* gettype(): 接受一个变量，判断变量的类型并返回字符串结果: bool, int, double, string, array, object, resource, NULL, or unknown。函数原型：`string gettype(mixed var);`

* settype(): 改变一个变量的类型。函数原型：`bool settype(mixed var, string type);`

* is_array(): 检查变量是否是数组类型

* is\_double(), is\_float(), is_real(): 检查变量是否是浮点数类型

* is\_long(), is\_int(), is_integer(): 检查变量是否是整数类型

* is_string(): 检查变量是否是字符串类型

* is_bool(): 检查变量是否是布尔类型

* is_object(): 检查变量是否是对象对象

* is_resource(): 检查变量是否是资源类型

* is_null(): 检查变量是否是NULL类型

* is_scalar(): 检查变量是否是标量类型，标量类型包含整数类型、布尔类型、字符串类型和浮点类型

* is_numeric(): 检查变量是否是一个数字或者数字字符串

* is_callable(): 检查变量是否是一个有效的函数名

## 测试变量状态的函数

* isset(): 该函数可以接受一个变量，如果该变量存在则返回`true`，否则返回`false`；也可以接收逗号分隔的变量列表，只有当所有变量都存在时才返回`true`。

* unset(): 该函数用于销毁一个或多个变量，使它们不再存在。

* empty(): 该函数用于判断一个变量是否存在，并且是否非空、非零值。

## 重新解释变量的函数

* `int intval(mixed var[, int base]);`: 把指定变量的值转换成整数，还可以指定转换的基数(10进制、16进制还是8进制？)

* `float floatval(mixed var);`: 把指定变量的值转换成浮点数

* `string strval(mixed var);`: 把指定变量的值转换成字符串


