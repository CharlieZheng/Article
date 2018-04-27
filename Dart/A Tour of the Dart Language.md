[A Tour of the Dart Language](https://www.dartlang.org/guides/language/language-tour#a-basic-dart-program)

top-level functions：就是不需要类包裹的方法直接可以调用运行，像类 C 语言的 main 方法。

static and instance methods：与 top-level functions 相对应的

top-level variables：同上

static and instance variables：与 top-level variables 相对应的

nested or local functions：这个不知道是啥

没有 public protected private 关键字，当一个变量以 _ 开头时则它是 private 的

```dymatic name = "Charlie"``` 和 ```Object name = "Charlie"``` 声明的变量在后续的代码中其类型可以发生改变

而 ```String name = "Charlie"``` 这种明确的声明方式则不行

``` var name = "Charlie"``` 声明的 ```name``` 变量被推导为 String 类型，故其类型也不会发生改变

### 默认初始值
都为 null

```
int lineCount;
assert(lineCount == null);
```

assert 判断会在生产代码中被忽略不执行

### final const

const 一定是 final 的

const 可类比为 Java 中的 static

final 可类比为 Java 中的 final

但是，在 Java 中被 final 修饰的变量在声明的时候赋值，而 Dart 中则不需要而是在第一次被使用时赋值

a const variable is a compile-time constant，难道 final variable 就不是吗？

如果 const 变量是在类层级的，标记其为 static const

在 Dart 中也有 static 关键字啊，有点懵

并不知道 final 和 const 有何区别

### 内建类型

 - numbers
 - strings
 - booleans
 - lists(also known as arrays)
 - maps
 - runes(for expressing Unicode characters in string)
 - symbols

#### Numbers

##### int
不大于 8 个字节
##### double
8 个字节

map 有点直接就是一个 json 对象

其它的不看了直接来看新东西，Runes

##### Runes
