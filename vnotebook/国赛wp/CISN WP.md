# Web
## easyweb
非预期，admin空密码getflag,注意js限制了空密码，burp改包即可
# Misc
## 寻找入侵者

直接把所有地址拖出来做成一个字典跑一下  
aircrack-ng hanshake.cap看一下SSID  
密码和SSID都有了可以解密一下数据包  
airdecap-ng hanshake.cap -p 88:25:93:c1:c8:eb -e Honey  
分析解密后的数据包可以找到一个key.rar  
打开又是一个数据包，flag应该在里面了  
string一下  
看了下strings出来的数据，最后一行明显flag格式，去掉！试一下  
成功getflag