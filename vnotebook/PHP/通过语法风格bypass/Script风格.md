# Script风格
* 可以用``<script>``标签包含PHP代码。
* 只需要简单指定这个标签的``language``属性为``"php"``，就可以使用这种方法了。
```php
<script language="php">
    echo "Hello, world";
</script>
```
* 这种方法在只能解析严格的HTML标签，并且不支持XML处理命令的编辑器中，变得十分有用。
