# include
(PHP 4, PHP 5, PHP 7)

``include`` 语句包含并运行指定文件。

以下文档也适用于 ``require``。

被包含文件先按参数给出的路径寻找，如果没有给出目录（只有文件名）时则按照 ``include_path`` 指定的目录寻找。如果在`` include_path ``下没找到该文件则`` include ``最后才在调用脚本文件所在的目录和当前工作目录下寻找。如果最后仍未找到文件则 ``include`` 结构会发出一条警告；这一点和 ``require`` 不同，后者会发出一个致命错误。

如果定义了路径——不管是绝对路径（在 Windows 下以盘符或者 \ 开头，在 Unix/Linux 下以 / 开头）还是当前目录的相对路径（以 . 或者 .. 开头）——``include_path`` 都会被完全忽略。例如一个文件以 ../ 开头，则解析器会在当前目录的父目录下寻找该文件。

有关 PHP 怎样处理包含文件和包含路径的更多信息参见 ``include_path ``部分的文档。

当一个文件被包含时，其中所包含的代码继承了`` include ``所在行的变量范围。从该处开始，调用文件在该行处可用的任何变量在被调用的文件中也都可用。不过所有在包含文件中定义的函数和类都具有全局作用域。
```php
vars.php
<?php

$color = 'green';
$fruit = 'apple';

?>

test.php
<?php

echo "A $color $fruit"; // A

include 'vars.php';

echo "A $color $fruit"; // A green apple

?>
```