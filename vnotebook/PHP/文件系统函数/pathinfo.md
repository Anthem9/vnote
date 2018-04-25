# pathinfo
(PHP 4 >= 4.0.3, PHP 5, PHP 7)

pathinfo — 返回文件路径的信息

## 说明 
```php
mixed pathinfo ( string $path [, int $options = PATHINFO_DIRNAME | PATHINFO_BASENAME | PATHINFO_EXTENSION | PATHINFO_FILENAME ] )
```
``pathinfo() ``返回一个关联数组包含有`` path ``的信息。返回关联数组还是字符串取决于 ``options``。

## 参数
### path
要解析的路径。

### options
如果指定了，将会返回指定元素；它们包括：``PATHINFO_DIRNAME``，``PATHINFO_BASENAME`` 和 ``PATHINFO_EXTENSION`` 或 ``PATHINFO_FILENAME``。

如果没有指定 ``options ``默认是返回全部的单元。

## 返回值
如果没有传入`` options`` ，将会返回包括以下单元的数组 ``array``：``dirname``，``basename`` 和 ``extension``（如果有），以 及``filename``。

## 更新日志 
版本	说明
5.2.0 	添加了常量 ``PATHINFO_FILENAME``。
## 范例 
### Example #1 
``pathinfo()`` 例子
```php
<?php
$path_parts = pathinfo('/www/htdocs/inc/lib.inc.php');

echo $path_parts['dirname'], "\n";
echo $path_parts['basename'], "\n";
echo $path_parts['extension'], "\n";
echo $path_parts['filename'], "\n"; // since PHP 5.2.0
?>
```
以上例程会输出：
```php
/www/htdocs/inc
lib.inc.php
php
lib.inc
```
### Example #2 
``pathinfo()`` example showing difference between null and no extension
```php
<?php
$path_parts = pathinfo('/path/emptyextension.');
var_dump($path_parts['extension']);

$path_parts = pathinfo('/path/noextension');
var_dump($path_parts['extension']);
?>
```
以上例程的输出类似于：
```php
string(0) ""

Notice: Undefined index: extension in test.php on line 6
NULL
```