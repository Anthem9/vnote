# Bypass_a_Path_Based_Access_Control
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/Access_Control_Flaws/_v_images/_1525273137_22137.png)
## 技术概念或主题（Concept / Topic To Teach）
In a path based access control scheme, an attacker can traverse a path by providing relative path information. Therefore an attacker can use relative paths to access files that normally are not directly accessible by anyone, or would otherwise be denied if requested directly. 
## 技术原理（How It works）
大多数操作系统允许在路径中使用某些特征字符，如：
```
/etc/passwd
~/.ssh
./configure
./../../../etc/passwd
c:\boot.ini
.\test.txt
..\..\..\boot.ini
file:///c:\bbot.ini
c:\test~1.txt
```
## 总体目标（General Goals）
The user should be able to access a file that is not in the listed directory.
## 操作方法（Solutions）
* 选择一个文件，点击``View File``，然后抓包

![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/Access_Control_Flaws/_v_images/_1525274080_5639.png)

* 根据页面给出的文件目录，把文件名改为``../../../WEB-INF/spring-security.xml``，出现以下结果

![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/Access_Control_Flaws/_v_images/_1525274191_21917.png)

* 我的方法是正确的，但是这个危险的操作是被禁止的，改为``../ru/FailOpenAuthentication.html``

![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/Access_Control_Flaws/_v_images/_1525274852_29261.png)

* 还是不行，把``spring-security.xml``的权限改为``777``，仍然失败，gg。

* 