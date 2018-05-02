# WebGoat_HTTP_basics
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/_v_images/_1525221121_20356.png)
## 技术概念或主题（Concept / Topic To Teach）
This lesson presents the basics for understanding the transfer of data between the browser and the web application.
## 技术原理（How It works）
Client Request: How HTTP works:

All HTTP transactions follow the same general format. Each client request and server response has three parts: **the request or response line**, **a header section**, and **the entity body**. The client initiates a transaction as follows: 

The client contacts the server and sends a document request
```
        GET /index.html?param=value HTTP/1.0
```
Next, the client sends optional header information to inform the server of its configuration and the document formats it will accept.
```
        User-Agent: Mozilla/4.06 Accept: image/gif, image/jpeg, */* 
```
After sending the request and headers, the client may send additional data. This data is mostly used by CGI programs using the POST method.
## 总体目标（General Goals）
* Enter your name in the input field below and press "go" to submit. The server will accept the request, reverse the input, and display it back to the user, illustrating the basics of handling an HTTP request. 
* 练习使用抓包工具
## 操作方法（Solutions）
* 设置浏览器代理，抓包，没啥好说的