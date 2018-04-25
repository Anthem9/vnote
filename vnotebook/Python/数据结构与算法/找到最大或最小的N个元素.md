# 找到最大或最小的N个元素
## 问题
我们想在某个集合中找出最大或最小的N个元素。

## 解决方案
``heapq``模块中有两个函数——``nlargest()``和``nsmallest()``——它们正是我们所需要的。
```python
import heapq

nums = [1, 8, 2, 23, 7, -4, 18, 23, 42, 37, 2]
print(heapq.nlargest(3,nums))    # Prints [42, 37, 23]
print(heapq.nsmallest(3,nums))    #Prints [-4, 1, 2]
```
这两个函数都可以接受一个参数``key``，从而允许它们工作在更加复杂的数据结构之上。
```python
portfolio = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45,'price': 16.35},
    {'name': 'ACME', 'shares': 75,'price': 115.65}
]

cheap = heapq.nsmallest(3, portfolio, key=lambda s: s['price'])
expensive = heapq.nlargest[3, portfolio, key=lambda s: s['price']]
```
## 讨论
* 也可以先对列表排序然后切片。
* 关于``lambda``表达式
```python
>>> key = lambda s: s['price']
>>> key
<function <lambda> at 0x05E03EB0>
>>> key(portfolio[0])
91.1
```