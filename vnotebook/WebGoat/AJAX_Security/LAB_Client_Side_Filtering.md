# LAB_Client_Side_Filtering
## 技术概念或主题（Concept / Topic To Teach）
It is always a good practice to send to the client only information which they are supposed to have access to. In this lesson, too much information is being sent to the client, creating a serious access control problem. 
## 
## 技术原理（How It works）
服务端与客户端数据交互过程虽然被页面代码进行了隐藏，但仍然可以通过 Firebug、IEWatch、WebScarab 等工具捕获到，直接显示在用户眼前。
## 总体目标（General Goals）
For this exercise, your mission is exploit the extraneous information being returned by the server to discover information to which you should not have access. 
## 操作方法（Solutions）
### STAGE 1
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/AJAX_Security/_v_images/_1524728212_27520.png)

* Press F12 and find ``id="hiddenEmployeeRecords"``, then find ``id="112"``

### STAGE 2
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/AJAX_Security/_v_images/_1524729085_24441.png)

* Find clientSideFiltering.jsp file and modify the fields in the figure as shown below

![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/AJAX_Security/_v_images/_1524729109_7936.png)

