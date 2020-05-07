title: PHP学习笔记——数组
date: 2016-09-03 16:30:55
tags: [php]
categories: 编程技术
---
PHP中的数组支持以数字为索引的常规数组和以字符串为索引的关联数组，在其它编程语言中，通常会提供两种数据类型——Array和Map（或者叫Hash)，分开来表示，而在PHP中相当于把这两种数据类型合并成了一个——统称为数组（Array）。

## 常规数组

### 创建数组

常规的创建一个数组的方法如下：

```
$products = array( ‘Tires’, ‘Oil’, ‘Spark Plugs’ );
```

这就创建了一个有三个元素的数组$products。

如果你需要一个特殊的数组，它里面的元素是升序有规律递增的数字，那么可以使用`range()`自动创建这个数组，例如：

```
$numbers = range(1, 10);
```

这就产生了一个包含从1到10的10个元素的数组$numbers。如果你希望递增的步长不是默认的1，还可以通过可选的第三个参数来指定步长，例如：

```
$odds = range(1, 10, 2);
```

这样就产生了一个从1到10之间奇数的数组$odds。

而且`range()`函数也可以应用在字符的递增上。

### 访问数组元素

在PHP中使用数字索引访问一个数组的方式跟C语言中完全一样，都是使用类似于`$arrayvar[index_num]`这样的形式访问，而且索引以0为开始。

PHP作为一个动态语言，在使用上自然要比C语言这样的静态编译语言方便很多。对于PHP中的数组而言，你不需要在使用之前预先定义，用到时直接使用即可，如果该数组不存在，就会自动创建。比如下面的例子：

```
$employees[0] = "Tom";
$employees[1] = "Jerry";
$employees[2] = "Ray";
```

在给数组的第一个元素赋值时，如果数组$employees不存在，就会自动创建，然后拥有第一个元素。

### 遍历数组元素

对于数组来说，使用循环语句遍历数组元素非常方便。比如对于上面的数组$employees，我们要遍历它的元素列表并打印出来，如下：

```
for ($i = 0; $i < count($employees); $i++) 
{
    echo $employees[$i] . " ";
}
```

针对数组这种数据结构，PHP还专门提供了一个循环语句`foreach`，例子如下：

```
foreach ($employees as $employee)
{
    echo $employee . " ";
}
```

## 关联数组

### 创建数组

使用下面的形式创建一个关联数组：

```
$employees = array('Tom' => 20, 'Jerry' => 22, 'Ray' => 25);
```

在这个关联数组中，名字是Key，年龄是Value，通过名字可以直接找到他的年龄。

### 访问数组元素

对于关联数组，使用元素的Key作为索引就可以访问到元素的Value，如下：

```
echo $employees['Tom'];
$employees['Ray'] = 26;
```

同样，再首次使用关联数组时，如果不存在，就自动创建。

### 遍历数组元素

遍历关联数组的元素，通常有下面三种方式：

#### foreach

```
foreach ($employees as $name => $age)
{
    echo $name . " - " . $age . "<br />"；
}
```

#### each

```
while ($elem = each($employees))
{
    echo $elem['key'] . " - " . $elem['value'] . "<br />";
}
```

#### list

```
reset($employees);  //如果前面的代码也对$employees使用过each操作，那么当前元素可能不是指向第一个，可以使用reset重置到第一个元素
while (list($name, $age) = each($employees))
{
    echo "$name - $age<br />";
}
```

## 多维数组

PHP中的多维数组的概念跟其它语言中没有本质的区别，访问方式也基本一致，我们只是看一下PHP中的多维数组时如何创建的就可以了。

下面是一个创建二维数组的例子：

```
$products = array( array( 'TIR', 'Tires', 100 ), 
                   array( 'OIL', 'Oil', 10 ),
                   array( 'SPK', 'Spark Plugs', 4 ));
```

可见，我们定义了一个常规的一维数组，它的每一个元素又是一个常规的一维数组，这就构成了二维数组。遍历这样一个二维数组的代码如下：

```
for ($row = 0; $row < count($products); $row++) {
    for ($column = 0; $column < count($products[$row]); $column++) {
        print '|'.$products[$row][$column]; 
    }
    print "|\n"; 
}
```

同样，数组的元素可以是关联数组，如下：

```
$products = array( array( ‘Code’ => ‘TIR’, 
                          ‘Description’ => ‘Tires’,
                          ‘Price’ => 100 ),
                   array( ‘Code’ => ‘OIL’, 
                          ‘Description’ => ‘Oil’,
                          ‘Price’ => 10 ),
                   array( ‘Code’ => ‘SPK’,
                          ‘Description’ => ‘Spark Plugs’,
                          ‘Price’ =>4 )
                );
```

遍历这样一个二维数组的代码如下：

```
for ( $row = 0; $row < count($products); $row++){
    echo '|'.$products[$row]['Code'].'|'.$products[$row]['Description'].
        '|'.$products[$row]['Price']."|\n";
}
```

一次类推，就可以知道如何创建三维数组、四维数组、...、等等更多维的数组了。

## 数组排序

### `sort`/`rsort`

这一组排序函数针对一维的常规数组，其中`sort`函数对数组做升序操作，`rsort`函数对数组做降序操作。

### `asort`/`arsort`

这一组排序函数针对一维的关联数组，其中`asort`函数根据关联数组中元素的Value进行升序操作，`arsort`函数根据关联数组中元素的Value进行降序操作。

### `ksort`/`krsort`

这一组排序函数针对一维的关联数组，其中`ksort`函数根据关联数组中元素的Key进行升序操作，`krsort`函数根据关联数组中元素的Key进行降序操作。

### `usort`/`uasort`/`uksort`

对于简单的数组元素（数字或者字符串），PHP知道如何去比较它们的大小，但对于复杂的数组元素（元素又是数组或者对象的情况），PHP就不知道怎么去比较它们的大小了。这是就需要开发者自定义比较函数，然后使用`usort`函数来对多维数组进行操作了。

比如针对下面的二维数组：

```
$products = array( array( 'TIR', 'Tires', 100 ), 
                   array( 'OIL', 'Oil', 10 ),
                   array( 'SPK', 'Spark Plugs', 4 ));
```

如果我们希望这个数组按照元素中的第二个字段（商品描述）进行升序排列，那么应该怎么做呢？

首先，我们需要定义一个自定义的比较函数：

```
function compare($x, $y) {
    if ($x[1] == $y[1]) {
        return 0;
    } else if ($x[1] < $y[1]) {
        return -1;
    } else {
        return 1;
    }
}
```

然后就可以使用这个自定义的比较函数对上述数组进行升序操作了，如下：

```
usort($products, 'compare');
```

如果你又希望这个数组按照元素中的第三个字段（商品价格）进行升序操作，那么只需要把比较函数修改成下面这样：

```
function compare($x, $y) {
    if ($x[2] == $y[2]) {
        return 0;
    } else if ($x[2] < $y[2]) {
        return -1;
    } else {
        return 1;
    }
}
```

对于多维数组的降序操作，也是通过自定义的比较函数来完成的。例如上述二维数组希望按照商品价格进行降序操作，那么比较函数定义如下：

```
function compare($x, $y) {
    if ($x[2] == $y[2]) {
        return 0;
    } else if ($x[2] < $y[2]) {
        return 1;
    } else {
        return -1;
    }
}
```

可见，是升序还是降序，都是通过根据比较后返回的数值来决定的：0表示$x和$y相等，负数表示$x小于$y，正数表示$x大于$y。

同理，`uasort`和`uksort`是针对多维数组中的关联数组进行自定义排序的函数，原理和使用方法跟`usort`类似，可自行测试，不再赘述。

### `shuffle`

该函数对数组元素进行随机排序。

### `array_reverse`

该函数返回一个数组，包含的元素是输入数组的反序排列，源数组保持不变。

## 从文件中加载数组

如果文件中存储的是结构化的数据，比如订单数据（通常这种结构化的数据存储在关系型数据库中），那么直接把这类数据加载到数组中再进行处理就非常方便，我们可以使用`file`和`explode`这两个函数结合来做到处理文件中的结构化数据。

`file`函数的原型: `array file ( string $filename [, int $flags = 0 [, resource $context ]] )`
`explode`函数的原型：`array explode ( string $delimiter , string $string [, int $limit = PHP_INT_MAX ] )`

## 其它关于数组的操作

### 数组内的导航

在PHP中，创建的数组维护了一个指针——指向当前的元素，类似于C语言中的链表。通过操作这个指针可以随意的遍历数组元素。

当创建了一个新的数组时，当前指针指向数组的第一个元素，使用`current()`函数可以返回当前指针指向的元素。

使用`next()`和`each()`函数可以让当前指针向前移动，它们的区别在于：`next()`先向前移动指针，然后返回指针指向的新元素；`each()`是先返回当前指针指向的元素，然后再向前移动指针。

当指针移动到数组的中间位置时，你可以使用`reset()`或者`end()`把当前指针重置到数组的开头（指向第一个元素）或结尾(指向最后一个元素)，同时会返回第一个元素或最后一个元素。

如果希望反向遍历一个数组，就可以先使用`end()`让当前指针指向最后一个元素，然后使用`prev()`函数来向前遍历。`prev()`函数跟`next()`函数相对应，它把当前指针向前移动，然后返回新的元素。

下面我们看一个反向遍历数组的例子：

```
$array = array(1, 2, 3);
$value = end($array);
while ($value) {
    echo "$value\n";
    $value = prev($array);
}
```

### 针对数组每一个元素调用处理函数

通常，我们会遍历一个数组，对每一个数组元素进行操作或修改。遍历一个数组有很多方法：`for`、`while`、`each()`等等，这里介绍一个更加简洁高效的方式: `array_walk()`，它的原型如下：

`bool array_walk ( array &$array , callable $callback [, mixed $userdata = NULL ] )`

`array_walk()`接受一个自定义函数，用来对数组的每一个元素进行处理操作。这个自定义函数的原型大概样子如下：

`yourfunction(value, key, userdata)`

第一个参数是数组元素的值，第二个参数是数组元素的Key，第三个参数是在`array_walk()`调用中指定的第三个参数：用户数据。

下面是一个例子：

```
<?php
$fruits = array("d" => "lemon", "a" => "orange", "b" => "banana", "c" => "apple");

function test_alter(&$item1, $key, $prefix)
{
    $item1 = "$prefix: $item1";
}

function test_print($item2, $key)
{
    echo "$key. $item2<br />\n";
}

echo "Before ...:\n";
array_walk($fruits, 'test_print');

array_walk($fruits, 'test_alter', 'fruit');
echo "... and after:\n";

array_walk($fruits, 'test_print');
?>
```

### 数组元素的数量统计

php中，对于数组元素的数量统计，提供了三个函数：`count()`、`sizeof()`和`array_count_values()`。

其中`count()`和`sizeof()`的作用完全一样，都是返回数组中元素的数量。

而`array_count_values()`这个函数是对数组中的元素值进行次数统计，把一样元素的出现次数统计出来，然后返回一个关联数组。

我们看一个例子就明白了：

```
<?php
function myprint($value, $key) 
{
    echo "$key: $value\n";
}

$array = array(4, 5, 1, 2, 3, 1, 2, 1);
$ac = array_count_values($array);
array_walk($ac, 'myprint');
?>
```

运行的结果如下：
> 4: 1
5: 1
1: 3
2: 2
3: 1

### 把一个关联数组转换成普通的变量

对于关联数组，我们可以使用`extract()`函数把每一个key/value元素转换成一个普通变量，变量名是数组元素的key，变量值是数组元素的Value。

`extract()`的原型是：`int extract ( array &$array [, int $flags = EXTR_OVERWRITE [, string $prefix = NULL ]] )`

通过例子我们理解一下它的用法：

```
<?php
$size = "large";
$var_array = array("color" => "blue",
                   "size"  => "medium",
                   "shape" => "sphere");
extract($var_array, EXTR_PREFIX_SAME, "wddx");

echo "$color, $size, $shape, $wddx_size\n";

?>
```

数组$var_array有三个key/value元素，所以通过`extract()`处理会生成三个普通变量：$color、$size、$shape，但是在前面已经定义了一个变量$size，所以会产生冲突，而`extract()`函数的第二个参数就是控制在发生冲突时如何处理的，这里使用了选项：`EXTR_PREFIX_SAME`——表示当出现冲突时通过数组产生的变量会添加前缀，前缀通过`extract()`的第三个参数指定。
