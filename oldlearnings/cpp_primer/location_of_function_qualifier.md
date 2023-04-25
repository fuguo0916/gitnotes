### 默认值
可以放在声明处或定义处，但是只能出现依次，不过可以在不同的地方添加默认值。建议放在声明处，并把声明放在头文件里。
### inline
可以放在声明处，也可以放在定义处，也可以都放。类成员是默认`inline`的。建议放在定义处，因为`inline`是实现层面的事情，并把定义放在头文件里。注意`inline`函数可以被定义多次。`inline`函数的调用者应该知道函数是如何定义的，如果只知道如何声明的就生成不了内联的源代码。
### const
必须同时放在定义处和所有声明的位置，以保证是同一个变量。
### virtual
放在class body的声明处，放在定义处会有编译时错误。
### static
这里只讨论类的`static`成员。只能放在class body的声明处，放在定义处会有编译时错误。
### constexpr
`constexpr`函数是隐式内联的。要保证在编译的时候就可以得到返回值。形参可以不是常量，但是实参必须是常量。而且只能有一个`return`语句。
### 参考文献
[inline, const, virtual, static四个关键字使用时应该放在哪里](https://blog.csdn.net/flyingleo1981/article/details/7826401)

[C++中constexpr函数](https://blog.csdn.net/hou09tian/article/details/110470363)