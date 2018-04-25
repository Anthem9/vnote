# 对象持久化与pickle模块
* 常常需要将对象内容保存到一个文件中或从中进行还原。
* 解决该问题的方法之一是编写两个函数，以一种特殊格式在文件中读取和写入数据。
* 另一种方法是用pickle和shelve模块。
* pickle模块将对象序列化为一个字节流，这个字节流可以写入到文件并在以后进行还原。
* pickle的接口非常简单，只包含dump()和load()操作。
* 例如，以下代码将一个对象写入一个文件：
```python
import pickle
obj = SomeObject()
f = open(filename,'wb')
pickle.dump(obj,f)    # 将对象保存到f上
f.close()
```
* 要还原对象，可以使用以下代码：
```python
import pickle
f = open(filename,'rb')
pickle.load(f)
f.close()
```
* 可以依次发出一系列dump()操作来保存对象序列。
* 要还原这些对象，只需使用一个类似的load()操作序列。
* shelve模块类似于pickle，但它将对象保存在一个类似字典的数据库中：
```python
import shelve
obj = SomeObject()
db = shelve.open("filename")    # 打开一个shelve
db['key'] = obj                 # 将对象保存在shelve中
...
obj = db['key']                 # 检索对象
db.close()                      # 关闭shelve
```
* 尽管由shelve创建的对象类似于一个字典，但它也有一些限制。
* 首先，键必须是字符串。
* 其次，shelve中存储的值必须与pickle兼容。
* 大部分Python对象都可以这样存储，但具有特殊用途的对象（如文件和网络连接）需要保持一种内部状态，这种状态无法通过这种方式保存和还原。
* pickle使用的数据格式是Python所专用的。
* 但是，随着Python版本的升级，该格式也被多次改进。
* 可以使用pickle中dump(obj, file, protocol)操作的一个可选协议参数来选择协议。
* 默认情况下使用协议0.
* 这是最古老的pickle数据格式，它将对象存储为几乎所有Python版本都能理解的格式。
* 但是，这种格式与Python很多更先进的用户定义类特性（如solt）不兼容。
* 协议1和协议2使用一种更高效的二进制数据表示。
* 要使用这些替代协议，可以执行如下操作：
```python
import pickle
obj = SomeObject()
f = open(filename,'wb')
pickle.dump(obj,f,2)                          # 使用协议2保存
pickle.dump(obj,f,pickle.HIGHEST_PROTOCOL)    # 使用最先进的协议
f.close()
```
* 使用load()还原对象时，不必指定协议。
* 底层协议已被编码到文件中。
* 类似地，可以打开一个shelve来使用替代地pickle协议保存Python对象，如下所示：
```python
import shelve
db = shelve.open(filename,protocol=2)
...
```
