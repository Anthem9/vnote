# class语句
* **类**定义了一组属性，这些属性与一组叫做**实例**的对象相关且由其共享。
* 类通常是由函数（称为**方法**，method）、变量（称为**类变量**，class variable）和计算出的属性（称为**特性**，property）组成的集合。
```python
class Account(object):
    num_account = 0
    def __init__(self,name,balance):
        self.name = name
        self.balance = balance
        Account.num_account += 1
    def __del__(self):
        Account.num_account -= 1
    def deposit(self,amt):
        self.balance += amt
    def withdraw(self,amt):
        self.balance -= amt
    def inquiry(self):
        return self.balance
```
* 需要注意的是，``class``语句本身并不创建该类的任何实例。
* 类仅设置将在以后创建的所有实例使用的属性。
* 从这种意义上讲，可以将类看作一个蓝图。
* 类变量（如``num_accounts``）是可在类的所有实例之间共享的值。
* 上例中的``num_accounts``变量用于跟踪存在多少个``Account``实例。