# dir函数
* 内置的dir()函数能够返回由对象所定义的名称列表
* 如果这一对象是一个模块，则该列表会包括函数内所定义的函数，类与变量。
* 该函数接受参数。如果参数是模块名称，函数将返回这一指定模块的名称列表。
* 如果没有提供参数，函数将返回当前模块的名称列表。
* 要注意到dir()函数能对任何对象工作。例如运行``dir(str)``可以访问str类的属性