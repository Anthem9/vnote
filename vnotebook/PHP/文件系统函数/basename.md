# basename
basename
(PHP 4, PHP 5, PHP 7)

basename — 返回路径中的文件名部分

## 说明 
```php
string basename ( string $path [, string $suffix ] )
```
给出一个包含有指向一个文件的全路径的字符串，本函数返回基本的文件名。

## 参数 
### path
一个路径。

在 Windows 中，斜线（/）和反斜线（\）都可以用作目录分隔符。在其它环境下是斜线（/）。

### suffix
如果文件名是以 suffix 结束的，那这一部分也会被去掉。

## 返回值 
Returns the base name of the given path. 返回 path 的基本的文件名。

## 更新日志 
版本	说明
4.1.0	增加了参数 suffix
## 范例 
Example #1 basename() 例子
```php
<?php
echo "1) ".basename("/etc/sudoers.d", ".d").PHP_EOL;
echo "2) ".basename("/etc/passwd").PHP_EOL;
echo "3) ".basename("/etc/").PHP_EOL;
echo "4) ".basename(".").PHP_EOL;
echo "5) ".basename("/");
?>
```
以上例程会输出：
```php
1) sudoers
2) passwd
3) etc
4) .
5) 
```