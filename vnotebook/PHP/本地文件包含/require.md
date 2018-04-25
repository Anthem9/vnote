# require
(PHP 4, PHP 5, PHP 7)
``require ``和 ``include`` 几乎完全一样，除了处理失败的方式不同之外。``require ``在出错时产生`` E_COMPILE_ERROR ``级别的错误。换句话说将导致脚本中止而 ``include ``只产生警告``E_WARNING``，脚本会继续运行。
