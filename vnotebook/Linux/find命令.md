# find命令
## 实例

* 列出当前目录及子目录下所有文件和文件夹
```bash
find .
```
* 在/home目录下查找以.txt结尾的文件名
```bash
find /home -name "*.txt"
```
* 同上，但忽略大小写
```bash
find /home -iname "*.txt"
```