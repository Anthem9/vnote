# rename
(PHP 4, PHP 5, PHP 7)

rename — 重命名一个文件或目录

## 说明 
```php
bool rename ( string $oldname , string $newname [, resource $context ] )
```
尝试把 ``oldname ``重命名为 ``newname``。

## 参数 ¶
### oldname
Note:
用于`` oldname ``中的封装协议必须和用于 ``newname`` 中的相匹配。
### newname
新的名字。

### context
Note: 在 PHP 5.0.0 中增加了对上下文（``Context``）的支持。有关上下文（``Context``）的说明参见 ``Streams``。

## 返回值 ¶
成功时返回 ``TRUE``， 或者在失败时返回 ``FALSE``。

## 更新日志 ¶
|版本	|说明|
|--|--|
|5.3.1|	可以在 ``Windows ``上跨驱动器 ``rename()`` 文件。|
|5.0.0|	``rename()`` 也可用于某些 ``URL`` 封装协议。参见支持的协议和封装协议 的列表看看 ``rename()`` 支持哪些 ``URL ``封装协议。|
|4.3.3|	在有适当权限的时候 ``rename() ``已经能够在基于`` *nix`` 的系统中跨磁盘分区重命名文件。Warnings may be generated if the destination filesystem doesn't permit chown() or chmod() system calls to be made on files — for example, if the destination filesystem is a FAT filesystem.|
## 范例 ¶
### Example #1
``rename()`` 例子
```php
<?php
rename("/tmp/tmp_file.txt", "/home/user/login/docs/my_file.txt");
?>
```