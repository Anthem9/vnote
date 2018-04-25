# move_uploaded_file
(PHP 4 >= 4.0.3, PHP 5, PHP 7)

``move_uploaded_file`` — 将上传的文件移动到新位置

## 说明 
```php
bool move_uploaded_file ( string $filename , string $destination )
```
本函数检查并确保由`` filename ``指定的文件是合法的上传文件（即通过 PHP 的`` HTTP POST ``上传机制所上传的）。如果文件合法，则将其移动为由 ``destination ``指定的文件。

这种检查显得格外重要，如果上传的文件有可能会造成对用户或本系统的其他用户显示其内容的话。

## 参数 
### filename
上传的文件的文件名。

### destination
移动文件到这个位置。

## 返回值 
成功时返回 ``TRUE``。

如果`` filename`` 不是合法的上传文件，不会出现任何操作，``move_uploaded_file() ``将返回`` FALSE``。

如果`` filename`` 是合法的上传文件，但出于某些原因无法移动，不会出现任何操作，``move_uploaded_file()`` 将返回 ``FALSE``。此外还会发出一条警告。

## 范例 
###  Example #1 
Uploading multiple files
```php
<?php
$uploads_dir = '/uploads';
foreach ($_FILES["pictures"]["error"] as $key => $error) {
    if ($error == UPLOAD_ERR_OK) {
        $tmp_name = $_FILES["pictures"]["tmp_name"][$key];
        $name = $_FILES["pictures"]["name"][$key];
        move_uploaded_file($tmp_name, "$uploads_dir/$name");
    }
}
?>
```
## 注释
## Note:
``move_uploaded_file() ``对``安全模式``和 ``open_basedir`` 都是敏感的。不过，限制只针对`` destination ``路径，因为允许移动上传的文件名`` filename`` 可能会与这些限制产生冲突。``move_uploaded_file()`` 仅作用于通过 ``PHP`` 上传的文件以确保这个操作的安全性。
### Warning
如果目标文件已经存在，将会被覆盖。