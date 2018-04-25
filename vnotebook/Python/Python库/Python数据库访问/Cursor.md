# Cursor
* 为了在数据库上执行操作，首先必须创建一个连接``c``，然后调用``c.cursor()``方法创建``Cursor``对象。
* ``Cursor``的实例``cur``有一些用于执行查询的标准方法和属性：
```python
cur.callproc(procname [, parameters])
```

* 调用一个名为``procname``的存储过程，``parameter``是一个序列的值，用作该过程的参数。
* 函数结果也是一个序列，项数与``parameters``相同。
* 它本来是``paramters``的副本，函数执行后，每一个输出参数的值都被修改值替换。
* 如果该过程还生成一组输出，可以使用``fetch*()``方法来读取。
```python
cur.close()
```

* 关闭游标，防止再对其进行任何操作。
```python
cur.execute(query [, parameters])
```
* 在数据库上执行查询或``query``，命令。
* ``query``是一个包含命令（通常是SQL）的字符串，``parameters``是一个序列或映射，用于为查询字符串中的字符变量赋值。
```python
cur.executemany(query [, parametersequence])
```
* 重复执行查询或命令。
* ``query``是一个查询字符串，``parameters``是一哥参数序列。
* 这一序列的每一项都是一个序列或映射对象，它们均可以用于上面提到的``execute()``方法。
```python
cur.fetchone()
```
* 返回由``execute()``或``executemany()``生成的下一行结果集。
* 这一结果通常是列表或元组，包含结果中的不同列的值。
* 如果没有更多的行，返回``None``。
* 如果没有未处理完的结果或者如果以前执行的操作没有生成结果集，就会提示异常。
```python
cur.fetchmany([size])
```
* 返回结果行的序列（如元组列表）。
* ``size``是要返回的行数。
* 如果省略，``cur.arraysize``的值就会作为默认值使用。
* 实际返回的行数可能比请求的少。
* 如果没有更多的行，就会返回空的序列。
```python
cur.fetchall()
```
* 返回全部剩余结果行的序列（如元组列表）。
```python
cur.nextset()
```
* 放弃当前结果集中的所有剩余行，跳至下一个结果集（如果有）。
* 如果没有更多的结果集，返回``None``；
* 否则返回``True``，接着通过``fetch*()``操作从新集合中返回数据。
## 简单示例
```python
import sqlite3
conn = sqlite3.connect("dbfile")
cur = conn.cursor()

# 简单查询示例
cur.execute("select name, shares, price from portfolio where account=12345")

# 循环结果
while True:
    row = cur.fetchone()
    if not row:
        break
    name, shares, price = row
    ...

# 一种替代方法（使用迭代）
cur.execute("select name, shares, price from portfolio where account=12345")
for name, shares, price in cur:
    # 处理行
    ...
```

    