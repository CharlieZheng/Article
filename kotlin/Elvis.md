### ?:优先级
```
fun main(args: Array<String>) {
    val age: String? = null
    println(5 + (age?.length ?: 3))
    println(5 > age?.length ?: 3)
    if (age?.length?:3 > 100 || age?.length?:3 < 100) {
        println("Hello")
    }
    println(age?.length ?: 3 - 2) // 优先级无论谁高谁低执行结果都一样
}
```
运算优先级：(+) 大于 (?:) 大于 (>) 大于 (||)

可在这里[这里](https://try.kotlinlang.org/#/Examples/Hello,%20world!/Simplest%20version/Simplest%20version.kt)运行上面的代码。
