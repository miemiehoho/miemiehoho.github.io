![img](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/12/202112101252247.png)



Throwable对象可以分为两组：

- 一组是unchecked异常，异常处理机制往往不用于这组异常，包括:
  - Error类通常是指Java的内部错误以及如资源耗尽的错误。当Error(及其衍生类)发生时，我们不能在编程层面上解决Error，所以应该直接退出程序。
  - Exception类有特殊的一个衍生类RuntimeException。RuntimeException(及其衍生类)是Java程序自身造成的，也就是说，由于程序员在编程时犯错。RuntimeException完全可以通过修正Java程序避免。比如将一个类型的对象转换成没有继承关系的另一个类型，即ClassCastException。这类异常应该并且可以避免。
- 另一组是checked异常。这些类是由编程与环境互动造成程序在运行时出错。比如读取文件时，由于文件本身有错误，发生IOException。



参考文献：https://blog.csdn.net/sinat_42483341/article/details/103022933



什么时候需要 声明异常或者 捕获，什么时候不需要？

RuntimeException(及其衍生类)



Java中异常分为两类：

- 运行时错误（比如除0）
  - 不需要声明或者捕获

- 预期错误（比如用户输入错误）
  - 需要声明或者捕获