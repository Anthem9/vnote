# 安装第三方包gcc错误
>Python安装三方模块报错error: command 'gcc' failed with exit status 1 on CentOS
```
" error: command 'gcc' failed with exit status 1 ". 
```
安装失败，因为缺少Python开发和一些依赖项。

纠正GCC问题的最佳方法：

你需要重新安装gcc，gcc-c + +和依赖

## For python 2.7
```
$ sudo yum -y install gcc gcc-c++ kernel-devel

$ sudo yum -y install python-devel libxslt-devel libffi-devel openssl-devel

$ pip install "your python packet"
```

## For python 3.4
```

$ sudo apt-get install python3-dev

$ pip install "your python packet"
```