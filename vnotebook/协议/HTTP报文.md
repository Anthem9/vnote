# HTTP报文

## HTTP请求报文

```
GET /search?hl=zh-CN&source=hp&q=domety&aq=f&oq= HTTP/1.1 
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, application/vnd.ms-excel, application/vnd.ms-powerpoint, 
application/msword, application/x-silverlight, application/x-shockwave-flash, */* 
Referer: <http://www.google.cn/> 
Accept-Language: zh-cn 
Accept-Encoding: gzip, deflate 
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 2.0.50727; TheWorld) 
Host: [www.google.cn](http://www.google.cn/) 
Connection: Keep-Alive 
Cookie: PREF=ID=80a06da87be9ae3c:U=f7167333e2c3b714:NW=1:TM=1261551909:LM=1261551917:S=ybYcq2wpfefs4V9g;
NID=31=ojj8d-IygaEtSxLgaJmqSjVhCspkviJrB6omjamNrSm8lZhKy_yMfO2M4QMRKcH1g0iQv9u-2hfBW7bUFwVh7pGaRUb0RnHcJU37y- 
FxlRugatx63JLv7CWMD6UB_O_r
```

观察发现，报文是用普通的``ASCII``文本书写。

其次，报文有很多行，每行用一个回车换行符结束。 

第一行：请求行（``request line``），

第一行后面的行，为请求头部（``header``）。 

然后是一个空行，最下面是请求数据。

![](https://github.com/Anthem9/everyday/raw/master/image/593225-20151228140454010-1128568448.png)

###请求行

```
GET /search?hl=zh-CN&source=hp&q=domety&aq=f&oq= HTTP/1.1
```

**有3个字段：方法字段，URL字段，HTTP协议版本字段。** 

* 方法字段：``GET``, ``POST``, ``HEAD``, ``PUT``, ``DELETE``。
* 当浏览器请求一个对象时，使用``GET``方法，在``URL``字段填写该对象的``URL``地址。 
* ``HTTP``协议版本

#### **1).GET**

最常见的一种请求方式，当客户端要从服务器中读取文档时，当点击网页上的链接或者通过在浏览器的地址栏输入网址来浏览网页的，使用的都是``GET``方式。``GET``方法要求服务器将``URL``定位的资源放在响应报文的数据部分，回送给客户端。使用``GET``方法时，请求参数和对应的值附加在``URL``后面，利用一个问号``?``代表``URL``的结尾与请求参数的开始，传递参数长度受限制。例如，``/index.jsp?id=100&op=bind``,这样通过``GET``方式传递的数据直接表示在地址中，所以我们可以把请求结果以链接的形式发送给好友。以用``google``搜索``domety``为例，``Request``格式如下：

```
GET /search?hl=zh-CN&source=hp&q=domety&aq=f&oq= HTTP/1.1  
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, application/vnd.ms-excel, application/vnd.ms-powerpoint, 
application/msword, application/x-silverlight, application/x-shockwave-flash, */*  
Referer: <a href="http://www.google.cn/">http://www.google.cn/</a>  
Accept-Language: zh-cn  
Accept-Encoding: gzip, deflate  
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 2.0.50727; TheWorld)  
Host: <a href="http://www.google.cn">www.google.cn</a>  
Connection: Keep-Alive  
Cookie: PREF=ID=80a06da87be9ae3c:U=f7167333e2c3b714:NW=1:TM=1261551909:LM=1261551917:S=ybYcq2wpfefs4V9g; 
NID=31=ojj8d-IygaEtSxLgaJmqSjVhCspkviJrB6omjamNrSm8lZhKy_yMfO2M4QMRKcH1g0iQv9u-2hfBW7bUFwVh7pGaRUb0RnHcJU37y-
FxlRugatx63JLv7CWMD6UB_O_r  
```

可以看到，``GET``方式的请求一般不包含”请求内容”部分，请求数据以地址的形式表现在请求行。地址链接如下：

```
<a href="http://www.google.cn/search?hl=zh-CN&source=hp&q=domety&aq=f&oq=">http://www.google.cn/search?hl=zh-CN&source=hp
&q=domety&aq=f&oq=</a> 
```

地址中``?``之后的部分就是通过``GET``发送的请求数据，我们可以在地址栏中清楚的看到，各个数据之间用``&``符号隔开。显然，这种方式不适合传送私密数据。另外，由于不同的浏览器对地址的字符限制也有所不同，一般最多只能识别``1024``个字符，所以如果需要传送大量数据的时候，也不适合使用``GET``方式。

 

#### 2).POST

对于上面提到的不适合使用``GET``方式的情况，可以考虑使用``POST方``式，因为使用``POST``方法可以允许客户端给服务器提供较多信息。``POST``方法将请求参数封装在``HTTP``请求数据中，以``名称/值``的形式出现，可以传输大量数据，这样``POST``方式对传送的数据大小没有限制，而且也不会显示在``URL``中。还以上面的搜索``domety``为例，如果使用``POST``方式的话，格式如下：

```
POST /search HTTP/1.1  
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, application/vnd.ms-excel, application/vnd.ms-powerpoint, 
application/msword, application/x-silverlight, application/x-shockwave-flash, */*  
Referer: <a href="http://www.google.cn/">http://www.google.cn/</a>  
Accept-Language: zh-cn  
Accept-Encoding: gzip, deflate  
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 2.0.50727; TheWorld)  
Host: <a href="http://www.google.cn">www.google.cn</a>  
Connection: Keep-Alive  
Cookie: PREF=ID=80a06da87be9ae3c:U=f7167333e2c3b714:NW=1:TM=1261551909:LM=1261551917:S=ybYcq2wpfefs4V9g; 
NID=31=ojj8d-IygaEtSxLgaJmqSjVhCspkviJrB6omjamNrSm8lZhKy_yMfO2M4QMRKcH1g0iQv9u-2hfBW7bUFwVh7pGaRUb0RnHcJU37y-
FxlRugatx63JLv7CWMD6UB_O_r  

hl=zh-CN&source=hp&q=domety  
```

可以看到，``POST``方式请求行中不包含数据字符串，这些数据保存在”请求内容”部分，各数据之间也是使用``&``符号隔开。``POST``方式大多用于页面的表单中。因为``POST``也能完成``GET``的功能，因此多数人在设计表单的时候一律都使用``POST``方式，其实这是一个误区。``GET``方式也有自己的特点和优势，我们应该根据不同的情况来选择是使用``GET``还是使用``POST``。

 

#### **3).HEAD**

``HEAD``就像``GET``，只不过服务端接受到``HEAD``请求后只返回响应头，而不会发送响应内容。当我们只需要查看某个页面的状态的时候，使用``HEAD``是非常高效的，因为在传输的过程中省去了页面内容。

### 请求头部

请求头部提供的信息是``WEB``代理缓存所要求的。

请求头部由``关键字/值``对组成，每行一对，关键字和值用英文冒号``:``分隔。

```
Host: <a href="http://www.google.cn">www.google.cn</a>
```

定义了目标所在的主机。



```
Connection: Keep-Alive 
```

浏览器告诉服务器使用持久连接， ``close`` 则 使用非持久连接，即要求服务器在发送请求的对象后就关闭连接，



```
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 2.0.50727; TheWorld)  
```

定义用户代理，即向服务器发送请求的浏览器类型。`` Mozilla/4.0``为``Netscape``浏览器。因此服务器可以正确地为不同类型的用户代理实际发送相同的对象不同的版本。



```
Accept-Language: zh-cn  
```

表示用户想得到该对象的语言版本,**也可以作为用户地区的判断。**



**CLIENT-IP、X-FORWARDED-FOR、REMOTE_ADDR**

共同：后面加IP地址，**可以用于服务端获取客户端的IP。**

区别：

1. ``CLIENT-IP``头是有的，只是未成标准，不一定服务器都实现了。
2. ``X-FORWARDED-FOR`` 是有标准定义，用来识别经过HTTP代理后的客户端IP地址，格式：``clientip``,``proxy1``,``proxy2``。可以被修改欺骗。
3. ``REMOTE-ADDR`` 是可靠的， 它是最后一个跟你的服务器握手的``IP``，在``curl ``中也无法伪造是，相对比较安全的服务端ip获取方法。

### 空行

最后一个请求头之后是一个空行，发送回车符和换行符，通知服务器以下不再有请求头。

### 请求数据

请求数据不在``GET``方法中使用，而是在``POST``方法中使用。``POST``方法适用于需要客户填写表单的场合。与请求数据相关的最常使用的请求头是``Content-Type``和``Content-Length``。

## HTTP响应报文

```
HTTP/1.1 200 OK
Date: Sat, 31 Dec 2005 23:59:59 GMT
Connection: Keep-Alive 
Content-Type: text/html;charset=ISO-8859-1
Content-Length: 122

＜html＞
＜head＞
＜title＞Wrox Homepage＜/title＞
＜/head＞
＜body＞
＜!-- body goes here --＞
＜/body＞
＜/html＞1234567891011121314
```

``HTTP``响应也由三个部分组成，分别是：状态行、响应头部、空白行、响应实体。

如下所示，``HTTP``响应的格式与请求的格式十分类似：

```
＜status-line＞

＜headers＞

＜blank line＞

[＜response-body＞]

```



![](https://github.com/Anthem9/everyday/raw/master/image/%E4%B8%8B%E8%BD%BD.png)

 正如你所见，在响应中唯一真正的区别在于第一行中用状态信息代替了请求信息。状态行（``status line``）通过提供一个状态码来说明所请求的资源情况。

### 状态行

状态行有3个字段：协议版本，状态码，相应状态信息。

```
HTTP/1.1 200 OK
```

在这个例子中，状态行指示服务器使用的协议是``HTTP/1.1``，并且一切正常(服务器已找到并正在发送所请求的对象)

状态行格式如下：

```
HTTP-Version Status-Code Reason-Phrase CRLF
```

其中，``HTTP-Version``表示服务器HTTP协议的版本；``Status-Code``表示服务器发回的响应状态代码；``Reason-Phrase``表示状态代码的文本描述。状态代码由三位数字组成，第一个数字定义了响应的类别，且有五种可能取值。

- 1xx：指示信息--表示请求已接收，继续处理。
- 2xx：成功--表示请求已被成功接收、理解、接受。
- 3xx：重定向--要完成请求必须进行更进一步的操作。
- 4xx：客户端错误--请求有语法错误或请求无法实现。
- 5xx：服务器端错误--服务器未能实现合法的请求。

常见状态代码、状态描述的说明如下。

- 200 OK：客户端请求成功。
- 400 Bad Request：客户端请求有语法错误，不能被服务器所理解。
- 401 Unauthorized：请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用。
- 403 Forbidden：服务器收到请求，但是拒绝提供服务。
- 404 Not Found：请求资源不存在，举个例子：输入了错误的URL。
- 500 Internal Server Error：服务器发生不可预期的错误。
- 503 Server Unavailable：服务器当前不能处理客户端的请求，一段时间后可能恢复正常，举个例子：HTTP/1.1 200 OK（CRLF）。

### 响应头部

```
Connection: close
```

这个响应头部告诉客户机在报文发送完后关闭了该TCP连接。

* ``Date``：指示服务器产生并发送该相应报文的日期和时间。这个时间是服务器从他的文件系统中检索到该对象。插入到响应报文并发送响应报文的时间。 


* ``Server``:表明该报文是由一个什么服务器产生的，类似于请求报文中的User-agent:请求头部 


* ``Last-Modified``:表示对象创建会最后修改的日期和时间，这个首部行对可能在客户机也可能在网络缓存服务器上的对象缓存很重要。 
* ``Content-Length``:表明被发送对象的字节数。 
* ``Content-Type``:指示了实体主体中的对象时什么类型的文本如HTML文本，JSON对象文本



#### **关于HTTP请求GET和POST的区别**

 

##### 1. 区别

``GET``提交，请求的数据会附在URL之后（就是把数据放置在HTTP协议头``＜request-line＞``中），以``?``分割``URL``和传输数据，多个参数用&连接;例如：

```url
login.action?name=hyddd&password=idontknow&verify=%E4%BD%A0 %E5%A5%BD
```

如果数据是英文字母/数字，原样发送，如果是空格，转换为``+``，如果是中文/其他字符，则直接把字符串用``BASE64``加密，得出如： ``%E4%BD%A0%E5%A5%BD``，其中``％XX``中的``XX``为该符号以``16进制``表示的``ASCII``。

``POST``提交：把提交的数据放置在是``HTTP``包的包体``＜request-body＞``中。上文示例中红色字体标明的就是实际的传输数据

因此，``GET``提交的数据会在地址栏中显示出来，而``POST``提交，地址栏不会改变

 

##### 2. 传输数据的大小：

首先声明,``HTTP``协议没有对传输的数据大小进行限制，``HTTP``协议规范也没有对URL长度进行限制。 而在实际开发中存在的限制主要有：

``GET``:特定浏览器和服务器对``URL``长度有限制，例如``IE``对``URL``长度的限制是2083字节(2K+35)。对于其他浏览器，如``Netscape``、``FireFox``等，理论上没有长度限制，其限制取决于操作系统的支持。

因此对于``GET``提交时，传输数据就会受到``URL``长度的限制。

``POST``:由于不是通过``URL``传值，理论上数据不受限。但实际各个``WEB``服务器会规定对``post``提交数据大小进行限制，``Apache``、``IIS6``都有各自的配置。



##### 3. 安全性：

​    ``POST``的安全性要比``GET``的安全性高。注意：这里所说的安全性和上面``GET``提到的“安全”不是同个概念。上面“安全”的含义仅仅是不作数据修改，而这里安全的含义是真正的``Security``的含义，比如：通过``GET``提交数据，用户名和密码将明文出现在``URL``上，因为
(1)登录页面有可能被浏览器缓存，
(2)其他人查看浏览器的历史纪录，那么别人就可以拿到你的账号和密码了，