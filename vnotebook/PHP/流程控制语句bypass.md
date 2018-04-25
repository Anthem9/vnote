# 流程控制语句bypass
```PHP
if ($user_validated):
    echo "Welcome!";
else:
    echo "Access Forbidden!"
endif;
```
```php
while(expr):
    statement;
    more statements;
endwhile;
```
```php
for(expr1;expr2;expr3):
    statement;
    ...;
endfor;
```
```php
foreach ($array as $current):
    ...;
endforeach
```
