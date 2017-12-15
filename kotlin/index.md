# 运算符重载
 - plus // 加法(a+b)
 - times // 乘法(a*b)
 - unaryMinus // 负数(-a)
 - inc // a++或者++a
 - contains // a in b
# 参数类型约束

#### where关键字可指定范型参数类型为多个指定类型
```
fun <T> ensureTrailingPeriod(seq: T)
        where T : CharSequence, T : Appendable
```
#### out 可指定为某类型的子类型
```
fun <T> copyData(source: MutableList<out T>,
                 destination: MutableList<T>)
```
#### in 与 out 相对
