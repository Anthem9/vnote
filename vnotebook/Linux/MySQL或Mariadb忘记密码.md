# MySQL或Mariadb忘记密码
摘要: ``mysql``和它的分支``mariadb``，是我们常用的开源数据库之一，有时候我们忘记了密码，或者数据库被入侵者修改了密码，该如何处理呢，本文即描述了解决步骤。
## 一、停止mysql或者mariadb服务
```
service mysqld|mariadb stop
```
## 二、跳过授权表启动
```
mysqld_safe --skip-grant-table &
```
## 三、修改mysql.user表的密码
不用密码登陆 
```
mysql -u root 
```
```
use mysql;
update user set password=password('newpass') where user='root';
exit;
```
## 四、杀掉mysqld进程
```
pkill msqld
```
## 五、重启服务
```
service mysqld|mariadb start
```