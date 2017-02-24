进入对应需要打jar的目录，输入命令: jar -cvf lkf.jar *.* （注意空格）

jar 是打jar的命令符；

-cvf 是打jar时的参数，写上就可以；

lkf.jar 是打成后的jar包名称；

*.* 是指将当前目录所有的文件都打入jar包，也可以输入*.class等。

例子：

现需要将C:\workspace\Auto\target\classes目录下的文件打jar包

1.打开cmd,cd C:\workspace\Auto\target\classes进入目录

2.输入命令: jar -cvf lkf.jar *.*或是jar -cvf lkf.jar *

3.C:\workspace\Auto\target\classes目录下lkf.jar