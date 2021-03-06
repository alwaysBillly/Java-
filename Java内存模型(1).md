# Java内存模型（一）

> 这里缺少一张图片


## 堆（heap）

    堆，是指用于存放Java对象的内存区域。
    1. JVM的堆被同一个JVM实例中的所有线程共享；
    2. 堆通常被某种自动内存管理机制所管理，这种机制一般叫做“垃圾回收”（garbage collection，GC）；
    3. Java堆中还可以细分为：新生代（Eden空间、From Survivor和To Survivor空间）和老年代。

## 栈（stack）

    1. JVM规定每个Java线程拥有自己独立的JVM栈，也就是Java方法的调用栈（call stack）。
    2. 同时JVM还规定了每个Java线程拥有自己独立的native方法栈（为了允许native方法调用Java方法，以及允许Java方法调用native方法）；
    3. 但在具体的JVM实现中，以Hotspot VM为例，它使用“mixed stack”——在同一个调用栈里面存放Java方法的栈帧（stack frame）和native方法的栈帧，所以在Hotspot中，每个Java线程其实只有一个调用栈。
    4. 栈帧保存着一个方法的局部变量、操作数栈和常量池指针；
    5. 每一次方法调用创建一个栈帧，并压入栈中。

## 方法区

    1. 方法区用于保存JVM装载的类的信息；
    2. 包括类型的常量池；类的字段、方法信息；方法的字节码；
    3. JDK6时，字符串常量池位于方法区中，JDK7时移动到了堆中；
> 为什么函数调用要用栈实现？
>   1. 函数调用的局部状态之所以用栈来记录是因为这些数据的存活时间正好满足“后进先出”（LIFO）的顺序，而栈的基本操作正好支持这种顺序。 
>   2. 下面来举例说明：
>
>```java
>int main() {
>  a();
>  return 0;
>}
>void a() {
>  b();
>}
>void b() {
>  c();
>}
>void c() {
>}
>```
>上面整个程序的函数活动时间可以表示为以下方式
>```t
>main()  a()  b()  c()
>   ——                         main()
>   |
>   +>     -                    a()
>   .      |
>   .      +>   -                 b()
>   .      .    |
>   .      .    +>   -              c()
>   .      .    .    |
>   .      .    +   <-              return from c()
>   .      .    |
>   .      +   <-                 return from b()
>   .      |
>   +     <-                    return from a()
>   |
>   ——                        return from main()
>```
>解释一下，也就是说先开始运行的方法main最后return，而最后开始的方法c第一个return，这就符合栈的“先进后出”操作；
