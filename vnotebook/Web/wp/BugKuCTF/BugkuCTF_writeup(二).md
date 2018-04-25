# BugkuCTF_writeup(二)

##Web篇

### 计算器

![Snipaste_2018-03-11_19-36-19](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_19-36-19.png)

打开题目地址

![Snipaste_2018-03-11_19-38-30](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_19-38-30.png)

要求输入31，但输入时却发现只能输入一个数字，查看一下源代码

![Snipaste_2018-03-11_19-39-52](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_19-39-52.png)

发现是在HTML中对输入长度进行了限制，HTML是客户端浏览器处理的，也就是说我们可以随意修改，这里有两个思路

#### 思路一

直接使用开发者工具进行前端的修改

![Snipaste_2018-03-11_19-43-12](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_19-43-12.png)

修改完就可以输入31了

![Snipaste_2018-03-11_19-44-06](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_19-44-06.png)

得到flag

![Snipaste_2018-03-11_19-44-49](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_19-44-49.png)

#### 思路二

使用Burp进行改包

考虑使用Burp将输入的3改成31，那么输入3点击验证

![Snipaste_2018-03-11_19-48-57](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_19-48-57.png)

直接弹出输入有误，Burp也没有抓到包，显然这是用JS在客户端验证的。。。

所以对于这个题来说这个思路不可行，但是！既然是在客户端验证的，那我们是不是能找到它的JS源码呢？

#### 由思路二得来的思路三

我们再次查看源代码

![Snipaste_2018-03-11_19-54-09](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_19-54-09.png)

找到这么一个东西，打开

![Snipaste_2018-03-11_19-54-57](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_19-54-57.png)

果然，【手动滑稽】

### web基础$_GET

![Snipaste_2018-03-11_19-57-51](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_19-57-51.png)

打开网址

![Snipaste_2018-03-11_19-59-22](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_19-59-22.png)

使用GET方式提交一个what变量，值为'flag'即可，既然是GET，直接在URL里写了

![Snipaste_2018-03-11_20-01-34](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_20-01-34.png)

### web基础$_POST

![Snipaste_2018-03-11_20-03-34](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_20-03-34.png)

打开网址

![Snipaste_2018-03-11_20-03-53](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_20-03-53.png)

使用POST方式提交一个what变量，值为'flag'

使用Burp抓个包

![Snipaste_2018-03-11_20-06-20](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_20-06-20.png)

需要改一下请求头，再添加一个变量

![Snipaste_2018-03-11_20-13-41](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_20-13-41.png)

### 矛盾

![Snipaste_2018-03-11_20-16-25](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_20-16-25.png)

打开网址

![Snipaste_2018-03-11_20-18-09](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_20-18-09.png)

用GET方式提交一个num变量，这个变量不能是数字，但它的值要等于1，这就是题目说的矛盾了。

考察了PHP弱类型的知识点，在我第一周的wp中有总结

![Snipaste_2018-03-11_20-22-21](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_20-22-21.png)

### Web3

![Snipaste_2018-03-11_20-24-38](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_20-24-38.png)

打开题目

![Snipaste_2018-03-11_20-25-06](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_20-25-06.png)

不停的弹窗，直接看源码了（具体方法见第二周）

![Snipaste_2018-03-11_20-26-43](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_20-26-43.png)

HTML编码，拿去解码

![Snipaste_2018-03-11_20-28-27](BugKuCTF_writeup(二)_photos\Snipaste_2018-03-11_20-28-27.png)

在线解码网址

http://tool.oschina.net/encode

## 靶场链接&资源

BugKu

http://ctf.bugku.com/