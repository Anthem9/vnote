# SGML风格 短标签
```php
<? echo "Hello， world"; ?>
```

* 这种形式被称为**短标签**，默认是关闭的。
* 想要PHP支持短标签，可以在编译PHP时，增加``-enable-short-tags``选项，
* 或者在PHP配置文件中打开``short_open_tag``。

```php
<?= ... ?>
```

* 这种``echo``短标签无论短标签是否打开，都是可用的。