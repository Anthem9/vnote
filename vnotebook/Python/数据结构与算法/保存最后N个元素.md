# 保存最后N个元素
## 问题
我们希望在迭代或是其他形式的处理过程中对最后几项记录做一个有限的历史记录统计。

## 解决方案
* 保存有限的历史记录可算是collections.deque的完美应用场景了。
* 例如，下面的代码对一系列文本行做简单的文本匹配操作，当发现有匹配时就输出当前的匹配行以及最后检查过的N行文本。
```python
from collections import deque

def search(lines, pattern, history=5):
    previous_lines = deque(maxlen=history)
    for line in lines:
        yield line, previous_lines
    previous_lines.append(line)

# Example use on a file
if __name__ == '__main__':
    with open('somefile.txt') as f:
        for line, prevlines in search(f, 'python', 5):
            for pline in prevlines:
                print(pline, end='')
            print(line, end='')
            print('-'*20)
```
## 讨论
* 如同上面的代码片段中做的一样，当编写搜索某项记录的代码时，通常会用到含有yield关键字的生成器函数。
* 这将处理搜索过程的代码和使用搜索结果的代码成功解耦开来。
* deque(maxlen=N)创建了一个固定长度的队列。
* 当有新纪录加入而队列已满时会自动移除最老的那条记录。
```python
>>> q = deque(maxlen=3)
>>> q.append(1)
>>> q.append(2)
>>> q.append(3)
>>> q
deque([1,2,3],maxlen=3)
>>> q.append(4)
>>> q
deque([2,3,4],maxlen=3)
```

* 更普遍的是，当需要一个简单的队列结构时，deque可祝你一臂之力。
* 如果不指定队列的大小，也就得到了一个无界限的队列，可以在两端执行添加和弹出操作。
```python
>>> q
deque([1,2,3])
>>> q.appendleft(4)
>>> q
deque([4,1,2,3])
>>> q.pop()
3
>>> q.popleft()
4
```
* 从队列两端添加或弹出元素的复杂度都是O(1)。
* 这和列表不同，当从列表的头部插入或移除元素时，列表的复杂度为O(N)。