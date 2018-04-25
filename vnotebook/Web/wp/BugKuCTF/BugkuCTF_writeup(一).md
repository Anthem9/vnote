

# BugkuCTF_writeup(一)

## Web篇

### Web2



![Snipaste_2018-03-11_10-56-13](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_10-56-13.png)

打开题目网址

![Snipaste_2018-03-11_10-59-39](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_10-59-39.png)

出现了大量的滑稽，考虑到题目说聪明的人都能找到答案，猜想flag在源代码中。

但发现右键没有查看源码的选项，这时候我们有三种思路

#### 思路一

直接在网址前添加``view-source：``

![Snipaste_2018-03-11_11-03-31](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_11-03-31.png)

回车

![Snipaste_2018-03-11_11-04-20](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_11-04-20.png)

得到flag

#### 思路二

禁用JS

打开Chrome的代理设置

![Snipaste_2018-03-11_11-05-37](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_11-05-37.png)

选择JS

![Snipaste_2018-03-11_11-06-26](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_11-06-26.png)

禁用之

![Snipaste_2018-03-11_11-07-36](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_11-07-36.png)

重新打开题目网址，发现可以右键查看源代码了，同样得到了flag

#### 思路三

直接F12打开开发者工具，查看源代码

#### 小结

很简单的一道查看源代码的题目，虽然简单但在CTF中却经常出现，实战意义是用来查看一些用JS做了限制的网站的源码，或者处理禁止复制网站内容的网站。

### 文件上传测试

![Snipaste_2018-03-11_11-10-54](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_11-10-54.png)

打开题目网址

![Snipaste_2018-03-11_11-11-31](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_11-11-31.png)

随便提交了一个文件，提示非PHP文件

![Snipaste_2018-03-11_11-12-46](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_11-12-46.png)

那我把后缀名改为``.php``好了，结果提示非图片文件

![Snipaste_2018-03-11_11-14-26](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_11-14-26.png)

那不是必须必须既是``php``文件又是``jpg``文件？

非也，既然是web题还是要考虑绕过的问题，这个题目模拟到实战可以是这样的：我们想上传``php``文件，但网站只允许上传图片文件，这时候我们怎么绕过呢？两种思路

#### 思路一

利用MIME验证的漏洞

我们先上传一个图片文件

![Snipaste_2018-03-11_12-53-54](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_12-53-54.png)

使用Burp抓一下包

![Snipaste_2018-03-11_12-58-04](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_12-58-04.png)

可以发现MIME类型是image/jpeg，这时我们修改文件名为``1.jpg.php``即可绕过

![Snipaste_2018-03-11_13-01-01](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_13-01-01.png)

这也验证了，服务器只进行了MIME类型的检验而忽略了后缀名的检验从而导致了文件上传漏洞。

#### 思路二

通过00截断绕过

![Snipaste_2018-03-11_13-18-27](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_13-18-27.png)

这是一个网上wp所写的方法，即修改文件名为``1.jpg%00.php``将其中``%00``URLdecode后发送，但他们的MIME类型还是image，也就是说他们没有控制变量，我将Content-Type删除后发现不能得到flag，也就是说这道题考的不是00截断这个点，这种wp可行的原因是误打误撞利用了MIME校验的漏洞。

## 文件上传拓展

### javascript验证突破

先随便上传一张截图，发现上传成功了

![Snipaste_2018-03-11_13-26-54](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_13-26-54.png)

再试试php文件，看会报出什么错

![Snipaste_2018-03-11_13-29-04](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_13-29-04.png)

弹窗了，自然想到是JS验证。那么禁用掉JS就可以了（在Web2中提到过如何禁用，其他浏览器大同小异）

![Snipaste_2018-03-11_13-31-58](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_13-31-58.png)

成功。

### MIME限制文件上传

方法同文件上传测试

![Snipaste_2018-03-11_14-19-34](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_14-19-34.png)

先将文件名改为``1.jpg``，用Burp抓包后再改为``1.php``即可，也可直接修改MIME类型。

### 扩展名限制文件上传

![Snipaste_2018-03-11_14-24-17](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_14-24-17.png)

先测试，1.jpg成功上传，1.php不允许上传。

####思路一

试一下大小写绕过，修改文件名为1.Php，上传成功

![Snipaste_2018-03-11_14-26-27](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_14-26-27.png)

#### 思路二

尝试在拓展名后添加空格

![Snipaste_2018-03-11_14-30-42](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_14-30-42.png)

上传成功！

![Snipaste_2018-03-11_14-31-57](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_14-31-57.png)

#### 思路三

加个数字，修改文件名为1.php4

![Snipaste_2018-03-11_14-34-00](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_14-34-00.png)

成功。

### 文件头校验限制文件上传

![Snipaste_2018-03-11_14-54-17](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_14-54-17.png)

这种题目通过检测文件头来判断文件类别

所以我们添加相应的文件头即可

![Snipaste_2018-03-11_14-56-03](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_14-56-03.png)

成功。

![Snipaste_2018-03-11_14-57-03](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_14-57-03.png)

### 过滤php限制文件上传

双写绕过，如``1.pphphp``

### 00截断文件上传

抓包，在箭头位置添加一个空格，再到16进制界面把空格对应的20改成00

或者把%00进行URL-decode

![Snipaste_2018-03-11_16-03-15](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_16-03-15.png)

看起来后缀名还是.png，但服务器读取到00时会认为结束了，也就是1.php

![Snipaste_2018-03-11_16-01-27](https://github.com/Anthem9/BugKuCTF_writeup/raw/master/BugKuCTF_writeup(%E4%B8%80)_photos/Snipaste_2018-03-11_16-01-27.png)



## 靶场链接&资源

BugKu

http://ctf.bugku.com/

BWVS

**链接: https://pan.baidu.com/s/1o94RVqq 密码: i7jd**

**https://github.com/bugku/BWVS**

DVWA

http://www.freebuf.com/sectool/102661.html