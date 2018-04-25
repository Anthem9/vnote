# in_array
(PHP 4, PHP 5, PHP 7)

``in_array`` — 检查数组中是否存在某个值

## 说明 
```php
bool in_array ( mixed $needle , array $haystack [, bool $strict = FALSE ] )
```
大海捞针，在大海（haystack）中搜索针（ needle），如果没有设置 strict 则使用宽松的比较。

## 参数 
### needle
待搜索的值。

Note:

如果 ``needle`` 是字符串，则比较是区分大小写的。

### haystack
待搜索的数组。

### strict
如果第三个参数 ``stric``t 的值为 ``TRUE ``则 ``in_array()`` 函数还会检查 ``needle`` 的类型是否和 ``haystack`` 中的相同。

## 返回值 
如果找到 ``needle`` 则返回 ``TRUE``，否则返回 ``FALSE``。

## 范例 
### Example #1 
``in_array()`` 例子
```php
<?php
$os = array("Mac", "NT", "Irix", "Linux");
if (in_array("Irix", $os)) {
    echo "Got Irix";
}
if (in_array("mac", $os)) {
    echo "Got mac";
}
?>
```
第二个条件失败，因为 ``in_array()`` 是区分大小写的，所以以上程序显示为：
```php
Got Irix
```
## Example #2
``in_array()`` 严格类型检查例子
```php
<?php
$a = array('1.10', 12.4, 1.13);

if (in_array('12.4', $a, true)) {
    echo "'12.4' found with strict check\n";
}

if (in_array(1.13, $a, true)) {
    echo "1.13 found with strict check\n";
}
?>
```
以上例程会输出：
```php
1.13 found with strict check
```
## Example #3
``in_array()`` 中用数组作为 ``needle``
```php
<?php
$a = array(array('p', 'h'), array('p', 'r'), 'o');

if (in_array(array('p', 'h'), $a)) {
    echo "'ph' was found\n";
}

if (in_array(array('f', 'i'), $a)) {
    echo "'fi' was found\n";
}

if (in_array('o', $a)) {
    echo "'o' was found\n";
}
?>
```
以上例程会输出：
```php
  'ph' was found
  'o' was found
```