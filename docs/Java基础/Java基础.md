## 关于 public

一个源文件只能有一个public修饰的类

且该public修饰的类名必须与文件名相同

并且一个源文件可以只有非public类





# try-with-resource语法

jdk1.7引入了一个语法叫try-with-resource语法，它的使用如下：

如果一个类实现了AutoCloseable接口，并行重写close方法。
那么这个类就可以写在try-catch的try后面的括号中，并且能在try-catch块执行后自动执行这个方法。
举例如下：

```java
public class TryWithResource implements AutoCloseable {

    private int age = 18;

    @Override
    public void close() throws Exception {
        System.out.println("this is close 方法");
    }

    public static void main(String[] args) {
        try (TryWithResource tryWithResource = new TryWithResource()) {
            System.out.println(tryWithResource.age);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```

执行这个方法，我们看到打印结果：

```
18
this is close 方法

```

很明显这样写和下面这种经典写法的效果是一样的，只是看上去能更加简便一些。

```java
public class TryWithResource{

    private int age = 18;

    public void close() throws Exception {
        System.out.println("this is close 方法");
    }

    public static void main(String[] args) {
        TryWithResource tryWithResource = new TryWithResource();
        try {
            System.out.println(tryWithResource.age);
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                tryWithResource.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}

```

，java编程中很多对于资源的操作，比如文件流等需要连接的资源，都需要在finally中手动关闭资源，但又怕关闭资源抛异常，所以最终就是finally块中又加了try-catch块，这个结构确实怎么看怎么不得劲，但没办法，JDK1.7之前只能只样写。
但是在JDK1.7后，就可以通过接口的方式，优雅的写这些代码了。
