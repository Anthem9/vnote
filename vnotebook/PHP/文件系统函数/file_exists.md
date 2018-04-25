# file_exists
(PHP 4, PHP 5, PHP 7)

file_exists — 检查文件或目录是否存在

## 说明 
```php
bool file_exists ( string $filename )
```
检查文件或目录是否存在。

## 参数 
``filename``
文件或目录的路径。

在 ``Windows ``中要用 ``//computername/share/filename ``或者 ``\\computername\share\filename`` 来检查网络中的共享文件。

## 返回值 
如果由`` filename`` 指定的文件或目录存在则返回 ``TRUE``，否则返回 ``FALSE``。
### Warning
如果因为安全模式的限制而导致不能访问文件的话，该函数会返回 ``FALSE``。然而，可以使用`` include` 来包含，如果文件在 ``safe_mode_include_dir`` 所指定的目录里。

#### Note:

The check is done using the real UID/GID instead of the effective one.

#### Note: 
因为 ``PHP``的整数类型是有符号整型而且很多平台使用 32 位整型，对 2GB 以上的文件，一些文件系统函数可能返回无法预期的结果 。

## 范例 
### Example #1 
测试一个文件是否存在
```php
<?php
$filename = '/path/to/foo.txt';

if (file_exists($filename)) {
    echo "The file $filename exists";
} else {
    echo "The file $filename does not exist";
}
?>
```
## 错误／异常 
失败时抛出``E_WARNING``警告。