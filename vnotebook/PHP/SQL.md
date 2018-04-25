# SQL
* SQL转义的方法相当简单——在**单引号**、**双引号**、**空字符**和**反斜杠**前面加一个反斜杠``\``
* ``addslashes()``函数可添加这些反斜杠，``stripslashes()``函数则删除它们：
```php
$string = <<< EOF
"It's never going to work," she cried,
as she hit the backslash (\) key.
EOF;
$string = addslashes($string);
echo $string;
echo "\n";
echo stripslashes($string);
```
```php
\"It\'s never going to work,\" she cried,
as she hit the backslash (\\) key.
"It's never going to work," she cried,
as she hit the backslash (\) key.
```
