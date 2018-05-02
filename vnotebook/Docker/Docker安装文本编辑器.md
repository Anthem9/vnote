# Docker安装文本编辑器
在使用``docker``容器时，有时候里边没有安装``vim``，敲``vim``命令时提示说：``vim: command not found``，这个时候就需要安装``vim``，可是当你敲``apt-get install vim``命令时，提示： 
```bash
        Reading package lists... Done 
        Building dependency tree       
        Reading state information... Done 
        E: Unable to locate package vim 
```
这时候需要敲：``apt-get update``，这个命令的作用是：同步 ``/etc/apt/sources.list`` 和 ``/etc/apt/sources.list.d`` 中列出的源的索引，这样才能获取到最新的软件包。 

等更新完毕以后再敲命令：``apt-get install vim``命令即可。

注：``apt-get install vi``也是找不到的。