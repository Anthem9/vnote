# isset
(PHP 4, PHP 5, PHP 7)

``isset`` — 检测变量是否已设置并且非 ``NULL``

## 说明 
```php
bool isset ( mixed $var [, mixed $... ] )
```
检测变量是否设置，并且不是 ``NULL``。

如果已经使用 ``unset()`` 释放了一个变量之后，它将不再是 ``isset()``。若使用`` isset() ``测试一个被设置成 ``NULL`` 的变量，将返回 ``FALSE``。同时要注意的是 ``null`` 字符``（"\0"）``并不等同于 PHP 的 ``NULL ``常量。

如果一次传入多个参数，那么 ``isset()`` 只有在全部参数都以被设置时返回 ``TRUE ``计算过程从左至右，中途遇到没有设置的变量时就会立即停止。

## 参数 
### var
要检查的变量。

### ...
其他变量。

## 返回值 
如果 ``var ``存在并且值不是 ``NULL`` 则返回 ``TRUE``，否则返回 ``FALSE``。

## 更新日志 
|版本	|说明|
|--|--|
|5.4.0	|检查字符的非数字偏移量将会返回`` FALSE``。|

## 范例 
### Example #1 
isset() 例子
```php
<?php

$var = '';

// 结果为 TRUE，所以后边的文本将被打印出来。
if (isset($var)) {
    echo "This var is set so I will print.";
}

// 在后边的例子中，我们将使用 var_dump 输出 isset() 的返回值。
// the return value of isset().

$a = "test";
$b = "anothertest";

var_dump(isset($a));      // TRUE
var_dump(isset($a, $b)); // TRUE

unset ($a);

var_dump(isset($a));     // FALSE
var_dump(isset($a, $b)); // FALSE

$foo = NULL;
var_dump(isset($foo));   // FALSE

?>
```
这对于数组中的元素也同样有效：
```php
<?php

$a = array ('test' => 1, 'hello' => NULL, 'pie' => array('a' => 'apple'));

var_dump(isset($a['test']));            // TRUE
var_dump(isset($a['foo']));             // FALSE
var_dump(isset($a['hello']));           // FALSE

// 键 'hello' 的值等于 NULL，所以被认为是未置值的。
// 如果想检测 NULL 键值，可以试试下边的方法。 
var_dump(array_key_exists('hello', $a)); // TRUE

// Checking deeper array values
var_dump(isset($a['pie']['a']));        // TRUE
var_dump(isset($a['pie']['b']));        // FALSE
var_dump(isset($a['cake']['a']['b']));  // FALSE

?>
```
### Example #2 
在字符串位移中使用`` isset()``

PHP 5.4 改变了传入字符串位移时 ``isset``() 的行为。
```php
<?php
$expected_array_got_string = 'somestring';
var_dump(isset($expected_array_got_string['some_key']));
var_dump(isset($expected_array_got_string[0]));
var_dump(isset($expected_array_got_string['0']));
var_dump(isset($expected_array_got_string[0.5]));
var_dump(isset($expected_array_got_string['0.5']));
var_dump(isset($expected_array_got_string['0 Mostel']));
?>
```
以上例程在PHP 5.3中的输出：
```php
bool(true)
bool(true)
bool(true)
bool(true)
bool(true)
bool(true)
```
以上例程在PHP 5.4中的输出：
```php
bool(false)
bool(true)
bool(true)
bool(true)
bool(false)
bool(false)
```
## 注释 
### Warning
``isset() ``只能用于变量，因为传递任何其它参数都将造成解析错误。若想检测常量是否已设置，可使用`` defined()`` 函数。

### Note: 
* 因为是一个语言构造器而不是一个函数，不能被 可变函数 调用。
* 如果使用 ``isset()`` 来检查对象无法访问的属性，如果`` __isset() ``方法已经定义则会调用这个重载方法。