# here文档
* 使用``heredoc``可以很容易地把多行字符串放在程序中，如下所示：
```php
$clerihew = <<< EndOfQuote
Sir Humphrey Davy
Abominated gravy.
He lived in the odium
Of having discovered sodium.
EndOfQuote;
echo $clerihew;
```
```php
Sir Humphrey Davy
Abominated gravy.
He lived in the odium
Of having discovered sodium.
```
* ``<<<``符号告诉PHP解析器你正在书写一个``heredoc``。
* 在``<<<``符号之间必须有一个空格，这样程序才能识别标识符。