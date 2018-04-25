# exit
``exit`` — 输出一个消息并且退出当前脚本

## 说明 
```php
void exit ([ string $status ] )
void exit ( int $status )
```
中止脚本的执行。 尽管调用了`` exit()``， ``Shutdown``函数 以及 ``object destructors ``总是会被执行。

``exit ``是个语法结构，如果没有 ``status ``参数要传入，可以省略圆括号。

## 参数 
### status
如果 ``status`` 是一个字符串，在退出之前该函数会打印`` status ``。

如果 ``status ``是一个`` integer``，该值会作为退出状态码，并且不会被打印输出。 退出状态码应该在范围``0``至``254``，不应使用被PHP保留的退出状态码``255``。 状态码``0``用于成功中止程序。

## 返回值 
没有返回值。

## 范例 
### Example #1 
``exit()`` 例子
```php
<?php

$filename = '/path/to/data-file';
$file = fopen($filename, 'r')
    or exit("unable to open file ($filename)");

?>
```
### Example #2 
``exit() ``状态码例子
```php
<?php

//exit program normally
exit;
exit();
exit(0);

//exit with an error code
exit(1);
exit(0376); //octal

?>
```
### Example #3 
无论如何，``Shutdown``函数与析构函数都会被执行
```php
<?php
class Foo
{
    public function __destruct()
    {
        echo 'Destruct: ' . __METHOD__ . '()' . PHP_EOL;
    }
}

function shutdown()
{
    echo 'Shutdown: ' . __FUNCTION__ . '()' . PHP_EOL;
}

$foo = new Foo();
register_shutdown_function('shutdown');

exit();
echo 'This will not be output.';
?>
```
以上例程会输出：
```php
 Shutdown: shutdown()
 Destruct: Foo::__destruct()
```