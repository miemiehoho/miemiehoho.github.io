![image-20220222185155431](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202221851517.png)

io模型：BIO、NIO、AIO

Netty又是对NIO的优化包装

tcp-->jdk io-->nio-->netty

NIO本来就是同步非阻塞的通信

Java NIO 由以下几个核心部分组成： - Channels - Buffers - Selectors 虽然 Java NIO 中除此之外还有很多类和组件，但 Channel，Buffer 和 Selector 构成 了核心的 API。其它组件，如 Pipe 和 FileLock，只不过是与三个核心组件共同使用的 工具类。

![image-20220222185945249](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202221859299.png)



## IO模型

1) I/O 模型简单的理解：就是用什么样的通道进行数据的发送和接收，很大程度上决定了程序通信的性能 

2)  Java 共支持 3 种网络编程模型/IO 模式：BIO、NIO、AIO

3)  Java BIO ： 同步并阻塞(传统阻塞型)，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器 端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销 【简单示意图】

   ![image-20220222195938332](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202221959415.png)

4) Java NIO ： 同步非阻塞，服务器实现模式为一个线程处理多个请求(连接)，即客户端发送的连接请求都会注 册到多路复用器上，多路复用器轮询到连接有 I/O请求就进行处理 【简单示意图】

   ![image-20220222204513822](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202222045938.png)

5) Java AIO(NIO.2) ： 异步非阻塞，AIO 引入异步通道的概念，采用了 Proactor 模式，简化了程序编写，有效 的请求才启动线程，它的特点是先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于连接数较 多且连接时间较长的应用

## BIO、NIO、AIO适用场景分析

1. BIO 方式适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4 以前的唯一选择，但程序简单易理解。

2. NIO方式适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，弹幕系统，服务器间通讯等。 编程比较复杂，JDK1.4 开始支持。（netty基于NIO，可以实现长连接）

   如果某个通道数据量大，就需要占用大量资源去处理它，如果同时有多个通道数据量大，会造成并发，因此适用于连接数目多且连接比较短（轻操作）的架构

3. AIO方式使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用 OS 参与并发操作， 编程比较复杂，JDK7 开始支持（底层是调用操作系统去参与并发操作，程序可以直接返回去做其他事情，等待回调即可）

## JAVA BIO NIO和AIO对比

简单介绍：

- Java BIO ： 同步并阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，当然可以通过线程池机制改善。
- Java NIO ： 同步非阻塞，服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。
- Java AIO(NIO.2) ： 异步非阻塞，服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理，

场景分析:

- BIO方式适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4以前的唯一选择，但程序直观简单易理解。
- NIO方式适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，并发局限于应用中，编程比较复杂，JDK1.4开始支持。
- AIO方式使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用OS参与并发操作，编程比较复杂，JDK7开始支持。

在高性能的I/O设计中，有两个比较著名的模式Reactor和Proactor模式，其中Reactor模式用于同步I/O，而Proactor运用于异步I/O操作。

一般来说I/O模型可以分为：同步阻塞，同步非阻塞，异步阻塞，异步非阻塞IO。

- 同步阻塞IO：在此种方式下，用户进程在发起一个IO操作以后，必须等待IO操作的完成，只有当真正完成了IO操作以后，用户进程才能运行。JAVA传统的IO模型属于此种方式！
- 同步非阻塞IO:在此种方式下，用户进程发起一个IO操作以后边可返回做其它事情，但是用户进程需要时不时的询问IO操作是否就绪，这就要求用户进程不停的去询问，从而引入不必要的CPU资源浪费。其中目前JAVA的NIO就属于同步非阻塞IO。
- 异步阻塞IO：此种方式下是指应用发起一个IO操作以后，不等待内核IO操作的完成，等内核完成IO操作以后会通知应用程序，这其实就是同步和异步最关键的区别，同步必须等待或者主动的去询问IO是否完成，那么为什么说是阻塞的呢？因为此时是通过select系统调用来完成的，而select函数本身的实现方式是阻塞的，而采用select函数有个好处就是它可以同时监听多个文件句柄，从而提高系统的并发性！
- 异步非阻塞IO:在此种模式下，用户进程只需要发起一个IO操作然后立即返回，等IO操作真正的完成以后，应用程序会得到IO操作完成的通知，此时用户进程只需要对数据进行处理就好了，不需要进行实际的IO读写操作，因为真正的IO读取或者写入操作已经由内核完成了。目前Java中还没有支持此种IO模型。



作者：简单一点点
链接：https://www.jianshu.com/p/45f84a946047
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## BIO

### 1、基本介绍

1) Java BIO 就是传统的 **java io 编程**，其相关的类和接口在 java.io 
1)  BIO(blocking I/O) ： **同步阻塞**，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需 要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，可以通过线程池机制改善(实 现多个客户连接服务器)。 【后有应用实例】
3) BIO 方式适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4 以前的唯一选择，程序简单易理解

### 2、工作机制

对 BIO 编程流程的梳理：

1) 服务器端启动一个 ServerSocket 
2) 客户端启动 Socket 对服务器进行通信，默认情况下服务器端需要对每个客户 建立一个线程与之通讯
3) 客户端发出请求后, 先咨询服务器是否有线程响应，如果没有则会等待，或者被拒绝
4) 如果有响应，客户端线程会等待请求结束后，再继续执行

![image-20220222205314705](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202222053776.png)

### 3、应用实例

实例说明： 

1) 使用 BIO 模型编写一个服务器端，监听 6667 端口，当有客户端连接时，就启动一个线程与之通讯。
2)  要求使用线程池机制改善，可以连接多个客户端.

3) 服务器端可以接收客户端发送的数据(telnet 方式即可)。 
3) 代码演示

```java
package io.bio;

import java.io.IOException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.*;

/**
 * @author miemiehoho
 * @date 2022/2/22 20:56
 */
public class BIOServer {

    public static void main(String[] args) throws IOException {
        // 线程池机制
        // 思路：
        // 1.创建线程池
        // 2.有一个客户端连接，就创建一个线程与之通讯
        ThreadPoolExecutor threadPool = new ThreadPoolExecutor(
                5,
                10,
                10,
                TimeUnit.SECONDS,
                new SynchronousQueue<Runnable>(),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
        );
        // 创建 serversocket
        ServerSocket serverSocket = new ServerSocket(6667);
        System.out.println("服务器启动了：" + Thread.currentThread().getName());
        while (true) {
            // 监听，等待客户端连接
            final Socket accept = serverSocket.accept();
            // 创建一个线程，与之通讯
            threadPool.execute(() -> {
                System.out.println("已连接到一个客户端:" + Thread.currentThread().getName());
                handler(accept);
            });
        }
    }

    // handler方法，和客户端通信
    private static void handler(Socket socket) {
        byte[] bytes = new byte[1024];
        try (
                // 通过socket获取输入流
                InputStream inputStream = socket.getInputStream();
        ) {
            // 循环读取客户端发送的数据
            while (true) {
                int read = inputStream.read(bytes);
                if (read != -1) {
                    // 输出客户端发送到数据,字节数组转字符串
                    System.out.println("线程：" + Thread.currentThread().getName() + "输出客户端发送的数据：" + new String(bytes, 0, read));
                } else {
                    break;
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }


}
```

### 4、问题分析

1) 每个请求都需要创建独立的线程，与对应的客户端进行数据 Read，业务处理，数据 Write 。
2) 当并发数较大时，**需要创建大量线程来处理连接**，系统资源占用较大。 
2) 连接建立后，如果当前线程暂时没有数据可读，则线程就阻塞在 Read 操作上，造成**线程资源浪费**



## NIO



### 1、概述

1. Java NIO 全称 **java non-blocking IO**，是指 JDK 提供的新 API。从 JDK1.4 开始，Java 提供了一系列改进的 输入/输出的新特性，被统称为 NIO(即 New IO)，是同步非阻塞的

2. NIO 相关类都被放在 **java.nio** 包及子包下，并且对原 java.io 包中的很多类进行改写。【基本案例】 

3. NIO 有三大核心部分：**Channel(通道)，Buffer(缓冲区), Selector(选择器)**

   ![image-20220222222558703](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202222225833.png)

4. **NIO是 面向缓冲区 ，或者面向 块 编程的**。数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后 移动，这就增加了处理过程中的灵活性，使用它可以提供非阻塞式的高伸缩性网络

5. **NIO是事件驱动的。**Java NIO 的非阻塞模式，使一个线程从某通道发送请求或者读取数据，但是它仅能得到目前可用的数据，如果 目前没有数据可用时，就什么都不会获取，**而不是保持线程阻塞**，所以直至数据变的可以读取之前，该线程可 以继续做其他的事情。 非阻塞写也是如此，一个线程请求写入一些数据到某通道，但不需要等待它完全写入， 这个线程同时可以去做别的事情。【后面有案例说明】

6. 通俗理解：NIO是可以做到用一个线程来处理多个操作的。假设有 10000 个请求过来,根据实际情况，可以分配 50 或者 100 个线程来处理。不像之前的阻塞 IO那样，非得分配 10000 个。

7. HTTP2.0 使用了多路复用的技术，做到同一个连接并发处理多个请求，而且并发请求的数量比HTTP1.1 大了好 几个数量级

### 2、应用实例

```java
package io.nio;

import java.nio.IntBuffer;

/**
 * @author miemiehoho
 * @date 2022/2/22 22:33
 */
public class BasicBuffer {

    public static void main(String[] args) {
        // 举例说明buffer的使用
        // 创建一个buffer，大小为5，即可以存放5个int
        IntBuffer intBuffer = IntBuffer.allocate(5);

        // 向buffer存放数据
        for (int i = 0; i < intBuffer.capacity(); i++) {
            intBuffer.put(i * 2);
        }

        // 从buffer读取数据
        // buffer读写切换(反转、重置)
        // filp主要是将操作数据的指针移动到开始和数据长度的位置方便读写且不会超出数据的有效长度
        intBuffer.flip();
        while (intBuffer.hasRemaining()) {
            System.out.println(intBuffer.get());
        }

    }
}
```



### 3、NIO 和 BIO 的比较

1) **BIO 以流的方式处理数据**,而 **NIO 以块的方式处理数据**,**块 I/O 的效率比流 I/O 高很多** 
1) **BIO 是阻塞的，NIO 则是非阻塞的**
3) BIO 基于字节流和字符流进行操作，而 NIO 基于 Channel(通道)和 Buffer(缓冲区)进行操作，数据总是从通道 读取到缓冲区中，或者从缓冲区写入到通道中。Selector(选择器)用于监听多个通道的事件（比如：连接请求， 数据到达等），因此**使用单个线程就可以监听多个客户端通道**



### 4、NIO 三大核心原理示意图

![image-20220222224915124](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202222249189.png)

1) 每个 channel 都会对应一个 Buffer 
2) 一个Selector 对应一个线程， 一个线程对应多个 channel(连接) 
3) 该图反应了有三个 channel 注册到 该 selector //程序 
4) 程序切换到哪个 channel 是由事件决定的, Event 就是一个重要的概念 
5) Selector 会根据不同的事件，在各个通道上切换 
6) Buffer 就是一个内存块 ， 底层是有一个数组 
7) 数据的读取写入是通过 Buffer, 这个和 BIO有本质区别 , BIO 中要么是输入流，或者是 输出流, 不能双向，但是**NIO的 Buffer 是可以读也可以写,** 需要 flip 方法切换；
8) **channel 是双向的**, 可以反映底层操作系统的情况, 比如 Linux 底层的操作系统通道就是双向的  



### 5、缓冲区(Buffer)

#### 5.1、基本介绍

缓冲区（Buffer）：缓冲区本质上是一个可以读写数据的内存块，可以理解成是一个**容器对象(这个对象含有一个数组)**，该对象提供了**一组方法**，可以更轻松地使用内存块，缓冲区对象内置了一些机制，能够跟踪和记录缓冲区的状态变化情况。

Channel 提供从文件、网络读取数据的渠道，但是读取或写入的数据都必须经由 Buffer，如图: 【后面举 例说明】

![image-20220222225919780](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202222259856.png)

#### 5.2、Buffer 类及其子类

1) 在 NIO 中，Buffer 是一个顶层父类，它是一个抽象类, 类的层级关系图（八大基本数据类型除Boolean外都有对应的实Buffer实现类）:

   ![image-20220222230630886](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202222306932.png)

2) Buffer 类定义了所有的缓冲区都具有的四个属性来提供关于其所包含的数据元素的信息:

```java
public abstract class Buffer {

    // Invariants: mark <= position <= limit <= capacity
    private int mark = -1;
    private int position = 0;
    private int limit;
    private int capacity;
```

![image-20220222231154409](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202222311539.png)

3. 每个Buffer的实现类都包含一个 hb数组，真正的数据就存储在这个数组中

```java
final int[] hb;
```

#### 5.3、Buffer 类相关方法一览

![image-20220222231707122](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202222317360.png)

#### 5.4、ByteBuffer

从前面可以看出对于 Java 中的基本数据类型(boolean 除外)，都有一个 Buffer 类型与之相对应，最常用的自然是 ByteBuffer 类（二进制数据，字节Buffer，而网络传输底层都是字节的形式），该类的主要方法如下：

![image-20220222232002360](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202222320532.png)



### 6、通道（Channel）





# NIO

## 1、Java NIO概述

Java NIO（New IO或 Non Blocking IO）是从 Java 1.4 版本开始引入的一个新的 IO API，可以替代标准的 Java IO API。NIO支持面向缓冲区的、基于通道的 IO 操 作。NIO将以更加高效的方式进行文件的读写操作

### 1.1 阻塞 IO

通常在进行同步 I/O操作时，如果读取数据，代码会阻塞直至有可供读取的数据。同 样，写入调用将会阻塞直至数据能够写入。传统的 Server/Client 模式会基于 TPR （Thread per Request）,服务器会为每个客户端请求建立一个线程，由该线程单独负 责处理一个客户请求。这种模式带来的一个问题就是**线程数量的剧增**，大量的线程会 增大服务器的开销。大多数的实现为了避免这个问题，都采用了**线程池模型**，并设置 线程池线程的最大数量，这由带来了新的问题，如果线程池中有 100 个线程，而有 100 个用户都在进行大文件下载，会导致第 101 个用户的请求无法及时处理，即便第 101 个用户只想请求一个几 KB 大小的页面。传统的 Server/Client 模式如下图所示：

![image-20220223144417163](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202231444083.png)

### 1.2 非阻塞 IO(NIO)

NIO 中非阻塞 I/O采用了**基于 Reactor 模式**的工作方式，I/O调用不会被阻塞，相反 是注册感兴趣的特定 I/O事件，如可读数据到达，新的套接字连接等等，在发生特定 事件时，系统再通知我们。**NIO中实现非阻塞 I/O的核心对象就是 Selector**，Selector 就是注册各种 I/O事件地方，而且当我们感兴趣的事件发生时，就是这个对 象告诉我们所发生的事件，如下图所示：

![image-20220223143738677](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202231438432.png)

从图中可以看出，当有读或写等任何注册的事件发生时，可以从 Selector 中获得相应 的 SelectionKey，同时从 SelectionKey 中可以找到发生的事件和该事件所发生的具 体的 SelectableChannel，以获得客户端发送过来的数据。



非阻塞指的是 IO 事件本身不阻塞,但是获取 IO 事件的 select()方法是需要阻塞等待的. 区别是阻塞的 IO 会阻塞在 IO 操作上, NIO阻塞在事件获取上,没有事件就没有 IO, 从 高层次看 IO 就不阻塞了.也就是说只有 IO 已经发生那么我们才评估 IO 是否阻塞,但是 select()阻塞的时候 IO 还没有发生,何谈 IO 的阻塞呢?**NIO 的本质是延迟 IO 操作到真 正发生 IO 的时候,而不是以前的只要 IO 流打开了就一直等待 IO 操作**。

![image-20220223143923682](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202231439724.png)



### 1.3 NIO 概述

Java NIO 由以下几个核心部分组成：

- Channels 
- Buffers 
- Selectors 

虽然 Java NIO 中除此之外还有很多类和组件，但 Channel，Buffer 和 Selector 构成 了核心的 API。其它组件，如 Pipe 和 FileLock，只不过是与三个核心组件共同使用的 工具类。

#### 1.3.1 Channel

首先说一下Channel，可以翻译成“通道”。Channel 和 IO 中的 Stream(流)是差不 多一个等级的。只不过 **Stream是单向的**，譬如：InputStream, OutputStream.而 **Channel 是双向的**，既可以用来进行读操作，又可以用来进行写操作。
NIO 中的 Channel 的主要实现有：FileChannel、DatagramChannel、 SocketChannel 和 ServerSocketChannel，这里看名字就可以猜出个所以然来：分别 可以对应文件 IO、UDP和 TCP（Server 和Client）。

#### 1.3.2 Buffer

NIO 中的关键Buffer 实现有：ByteBuffer, CharBuffer, DoubleBuffer, FloatBuffer, IntBuffer, LongBuffer, ShortBuffer，分别对应基本数据类型: byte, char, double, float, int, long, short。

#### 1.3.3 Selector

**Selector 运行单线程处理多个 Channel**，如果你的应用打开了多个通道，但每个连接的流量都很低，使用 Selector 就会很方便。例如在一个聊天服务器中。要使用Selector, 得向 Selector 注册Channel，然后调用它的 select()方法。这个方法会一直阻塞到某个注册的通道有事件就绪。一旦这个方法返回，线程就可以处理这些事件， 事件的例子有如新的连接进来、数据接收等。



##  第2章 Java NIO（Channel）

### 2.1、 Channel 概述

Channel 是一个通道，可以通过它读取和写入数据，它就像水管一样，网络数据通过 Channel 读取和写入。通道与流的不同之处在于**通道是双向的，流只是在一个方向上 移动**（一个流必须是 InputStream或者OutputStream的子类），而且**通道可以用于 读、写或者同时用于读写**。因为**Channel 是全双工的**，所以它可以比流更好地映射底 层操作系统的 API。
NIO 中通过 channel 封装了对数据源的操作，通过 channel 我们可以操作数据源，但 又不必关心数据源的具体物理结构。这个数据源可能是多种的。比如，可以是文件， 也可以是网络 socket。在大多数应用中，channel 与文件描述符或者 socket 是一一 对应的。**Channel 用于在字节缓冲区和位于通道另一侧的实体（通常是一个文件或套 接字）之间有效地传输数据。**



channel 接口源码

```java
public interface Channel extends Closeable {

    /**
     * Tells whether or not this channel is open.
     *Returns:true if, and only if, this channel is open
     */
    public boolean isOpen();

    /**
     * Closes this channel.
     * @throws  IOException  If an I/O error occurs
     */
    public void close() throws IOException;

}
```

与缓冲区不同，**通道 API 主要由接口指定。不同的操作系统上通道实现（Channel Implementation）会有根本性的差异**，所以通道 API 仅仅描述了可以做什么。因此很 自然地，**通道实现经常使用操作系统的本地代码**。通道接口允许您以一种受控且可移 植的方式来访问底层的 I/O服务。 **Channel 是一个对象，可以通过它读取和写入数据**。拿 NIO 与原来的 I/O 做个比 较，通道就像是流。**所有数据都通过 Buffer 对象来处理**。您永远不会将字节直接写入 通道中，相反，您是将数据写入包含一个或者多个字节的缓冲区。同样，您不会直接 从通道中读取字节，而是将数据从通道读入缓冲区，再从缓冲区获取这个字节。



#### 特点

**Java NIO 的通道类似流，但又有些不同：** 

- 既可以从通道中读取数据，又可以写数据到通道。但流的读写通常是单向的。 
- 通道可以异步地读写。 
- 通道中的数据总是要先读到一个Buffer，或者总是要从一个Buffer 中写入。 

正如上面所说，从通道读取数据到缓冲区，从缓冲区写入数据到通道。如下图所示：

![image-20220223145945551](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202231459600.png)

### 2.2、 Channel 实现

下面是 Java NIO 中最重要的 Channel 的实现： 

- FileChannel

- DatagramChannel 
- SocketChannel 
- ServerSocketChannel

（1）FileChannel 从文件中读写数据。 

（2）DatagramChannel 能通过UDP读写网络中的数据。 

（3）SocketChannel 能通过 TCP 读写网络中的数据。

（4）ServerSocketChannel 可以监听新进来的 TCP 连接，像Web服务器那样。对 每一个新进来的连接都会创建一个 SocketChannel。 

正如你所看到的，**这些通道涵盖了UDP 和 TCP 网络 IO，以及文件 IO**



### 2.3、 FileChannel 介绍和示例

FileChannel 类可以实现常用的 read，write 以及 scatter/gather 操作，同时它也提 供了很多专用于文件的新方法。这些方法中的许多都是我们所熟悉的文件操作

![image-20220223150347824](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202231503911.png)

下面是一个使用 FileChannel 读取数据到 Buffer 中的示例：

```java
    // FileChannel 读取数据到buffer中
    public static void main(String[] args) {
        try (
                // 创建FileChannel,    FileChannel没有直接方法创建，需要通过文件创建
                // 获取文件，开启读写模式
                RandomAccessFile accessFile = new RandomAccessFile("D:\\13686\\OneDrive - 365word\\miemiehoho\\Project\\practice\\src\\nio\\chapter02\\001.txt", "rw");
                FileChannel fileChannel = accessFile.getChannel();
        ) {
            // 创建Buffer
            ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
            // 读取数据到Buffer中
            int read = fileChannel.read(byteBuffer);
            while (read != -1) {
                System.out.print("读取了：" + read + "byte");
                byteBuffer.flip();// 读写转换
                while (byteBuffer.hasRemaining()) {
                    System.out.print((char) byteBuffer.get());
                }
                byteBuffer.clear();
                read = fileChannel.read(byteBuffer);
            }
            System.out.println();
            System.out.println("读取结束");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

**Buffer 通常的操作：**

-  将数据写入缓冲区 
- 调用 buffer.flip() 反转读写模式 
- 从缓冲区读取数据 
- 调用 buffer.clear() 或 buffer.compact() 清除缓冲区内容

### 2.4、FileChannel 操作详解 

#### 2.4.1 打开 FileChannel

在使用 FileChannel 之前，必须先打开它。但是，我们无法直接打开一个FileChannel，需要通过使用一个 InputStream、OutputStream或 RandomAccessFile(随机读取流) 来获取一个 FileChannel 实例。下面是通过 RandomAccessFile 打开 FileChannel 的示例：

```java
RandomAccessFile aFile = new RandomAccessFile("d:\\atguigu\\01.txt", "rw"); 
FileChannel inChannel = aFile.getChannel();
```

#### 2.4.2 从 FileChannel 读取数据

调用多个 read()方法之一  从 FileChannel 中读取数据。如：

```java
ByteBuffer buf = ByteBuffer.allocate(48); 
int bytesRead = inChannel.read(buf);
```

首先，分配一个Buffer。从 FileChannel 中读取的数据将被读到Buffer 中。然后，调 用 FileChannel.read()方法。该方法将数据从 FileChannel 读取到Buffer 中。read() 方法返回的 int 值表示了有多少字节被读到了 Buffer 中。如果返回-1，表示到了文件 末尾。

#### 2.4.3 向 FileChannel 写数据

使用 FileChannel.write()方法向 FileChannel 写数据，该方法的参数是一个 Buffer。 如：

```java
// 使用 FileChannel.write()方法向 FileChannel 写数据
public static void main(String[] args) {
    try (
            RandomAccessFile accessFile = new RandomAccessFile("D:\\13686\\OneDrive - 365word\\miemiehoho\\Project\\practice\\src\\nio\\chapter02\\1.txt", "rw");
            FileChannel fileChannel = accessFile.getChannel();
    ) {
        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
        byteBuffer.clear();
        String content = "hello channel!";
        byteBuffer.put(content.getBytes());
        byteBuffer.flip();
        while (byteBuffer.hasRemaining()) {
            fileChannel.write(byteBuffer);
        }
    } catch (Exception e) {
        e.printStackTrace();
    }

}
```

注意 FileChannel.write()是在while 循环中调用的。因为**无法保证write()方法一次能 向 FileChannel 写入多少字节，因此需要重复调用write()方法**，直到 Buffer 中已经没 有尚未写入通道的字节。

#### 2.4.4 关闭 FileChannel

用完 FileChannel 后必须将其关闭。如： 

```java
inChannel.close();
```

#### 2.4.5 FileChannel 的position 方法

有时可能需要在 FileChannel 的某个特定位置进行数据的读/写操作。可以通过调用 position()方法获取 FileChannel 的当前位置。也可以通过调用 position(long pos)方 法设置 FileChannel 的当前位置。
这里有两个例子:

long pos = channel.position(); 

channel.position(pos +123);

如果将位置设置在文件结束符之后，然后试图从文件通道中读取数据，读方法将返回1 （文件结束标志）。
**如果将位置设置在文件结束符之后，然后向通道中写数据，文件将撑大到当前位置并 写入数据。这可能导致“文件空洞”**，磁盘上物理文件中写入的数据间有空隙。

#### 2.4.6 FileChannel 的 size 方法

FileChannel 实例的 size()方法将返回该实例所关联文件的大小。如: 

```java
long fileSize = channel.size();
```

#### 2.4.7 FileChannel 的 truncate 方法

可以使用 FileChannel.truncate()方法截取一个文件。截取文件时，文件将中指定长度 后面的部分将被删除。如：

```java
 channel.truncate(1024); 
```

这个例子截取文件的前 1024 个字节。

#### 2.4.8 FileChannel 的 force 方法

**FileChannel.force()方法将通道里尚未写入磁盘的数据强制写到磁盘上**。出于性能方 面的考虑，操作系统会将数据缓存在内存中，所以无法保证写入到 FileChannel 里的 数据一定会即时写到磁盘上。要保证这一点，需要调用 force()方法。 

force()方法有一个boolean 类型的参数，指明是否同时将文件元数据（权限信息等） 写到磁盘上。

#### 2.4.9 FileChannel 的 transferTo 和 transferFrom 方法

通道之间的数据传输： 如果两个通道中有一个是 FileChannel，那你可以直接将数据从一个 channel 传输到 另外一个 channel。

##### （1）transferFrom()方法

FileChannel 的 transferFrom()方法可以将数据从源通道传输到 FileChannel 中（译 者注：这个方法在 JDK文档中的解释为将字节从给定的可读取字节通道传输到此通道 的文件中）。下面是一个 FileChannel 完成文件间的复制的例子：

```java
// FileChannel 的 transferFrom 方法
public static void main(String[] args) {
    try (
            RandomAccessFile accessFile1 = new RandomAccessFile("D:\\13686\\OneDrive - 365word\\miemiehoho\\Project\\practice\\src\\nio\\chapter02\\001.txt", "rw");
            FileChannel channelFrom = accessFile1.getChannel();

            RandomAccessFile accessFile2 = new RandomAccessFile("D:\\13686\\OneDrive - 365word\\miemiehoho\\Project\\practice\\src\\nio\\chapter02\\002.txt", "rw");
            FileChannel channelTo = accessFile2.getChannel();

    ) {
        long position = 0;
        long size = channelFrom.size();
        channelTo.transferFrom(channelFrom, position, size);
    } catch (Exception e) {
        e.printStackTrace();
    }
    System.out.println("over!");
}
```

方法的输入参数position 表示从 position 处开始向目标文件写入数据，count 表示 最多传输的字节数。如果源通道的剩余空间小于 count 个字节，则所传输的字节数要 小于请求的字节数。此外要注意，在 SoketChannel 的实现中，SocketChannel 只会 传输此刻准备好的数据（可能不足 count 字节）。因此，SocketChannel 可能不会将 请求的所有数据(count 个字节)全部传输到 FileChannel 中。

##### （2）transferTo()方法 

transferTo()方法将数据从 FileChannel 传输到其他的 channel 中。 下面是一个 transferTo()方法的例子：

```java
// FileChannel 的 transferTo 方法
public static void main(String[] args) {
    try (
            RandomAccessFile accessFile1 = new RandomAccessFile("D:\\13686\\OneDrive - 365word\\miemiehoho\\Project\\practice\\src\\nio\\chapter02\\001.txt", "rw");
            FileChannel channelFrom = accessFile1.getChannel();
            RandomAccessFile accessFile2 = new RandomAccessFile("D:\\13686\\OneDrive - 365word\\miemiehoho\\Project\\practice\\src\\nio\\chapter02\\003.txt", "rw");
            FileChannel channelTo = accessFile2.getChannel();
    ) {
        long position = 0;
        long size = channelFrom.size();
        channelFrom.transferTo(position, size, channelTo);
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```



### 2.4 Socket 通道

1）**新的 socket 通道类可以运行非阻塞模式并且是可选择的**，可以激活大程序（如 网络服务器和中间件组件）**巨大的可伸缩性和灵活性**。本节中我们会看到，再也没有为每个 socket 连接使用一个线程的必要了，也**避免了管理大量线程所需的上下文交换 开销**。**借助新的NIO类，一个或几个线程就可以管理成百上千的活动 socket 连接了 并且只有很少甚至可能没有性能损失**。所有的 socket 通道类(DatagramChannel、 SocketChannel 和 ServerSocketChannel)都继承了位于 java.nio.channels.spi 包中 的AbstractSelectableChannel。这意味着我们可以用一个 Selector 对象来执行 socket 通道的就绪选择（readiness selection）。

2）请注意 DatagramChannel 和 SocketChannel 实现定义读和写功能的接口而 ServerSocketChannel 不实现。**ServerSocketChannel 负责监听传入的连接和创建新 的 SocketChannel 对象，它本身从不传输数据**。

3）在我们具体讨论每一种 socket 通道前，您应该了解 **socket 和 socket 通道之间 的关系**。通道是一个连接 I/O服务导管并提供与该服务交互的方法。**就某个 socket 而 言，它不会再次实现与之对应的 socket 通道类中的 socket 协议 API，而 java.net 中 已经存在的 socket 通道都可以被大多数协议操作重复使用。**

**全部 socket 通道类**（DatagramChannel、SocketChannel 和 ServerSocketChannel）**在被实例化时都会创建一个对等 socket 对象**。这些是我们所 熟悉的来自 java.net 的类（Socket、ServerSocket 和 DatagramSocket），它们已 经被更新以识别通道。**对等 socket 可以通过调用 socket( )方法从一个通道上获取**。 此外，这三个 java.net 类现在都有 getChannel( )方法。

4）要把一个 socket 通道置于非阻塞模式，我们要依靠所有 socket 通道类的公有超级类：SelectableChannel。就绪选择（readiness selection）是一种可以用来查 询通道的机制，该查询可以判断通道是否准备好执行一个目标操作，如读或写。非阻 塞 I/O和可选择性是紧密相连的，那也正是管理阻塞模式的 API 代码要在 SelectableChannel 超级类中定义的原因。

设置或重新设置一个通道的阻塞模式是很简单的，只要调用 configureBlocking( )方 法即可，传递参数值为 true 则设为阻塞模式，参数值为 false 值设为非阻塞模式。可 以通过调用 isBlocking( )方法来判断某个 socket 通道当前处于哪种模式。

```java
// Lock for registration and configureBlocking operations
private final Object regLock = new Object();

// Adjusts this channel's blocking mode.
public final SelectableChannel configureBlocking(boolean block)
    throws IOException
{
    synchronized (regLock) {
        if (!isOpen())
            throw new ClosedChannelException();
        if (blocking == block)
            return this;
        if (block && haveValidKeys())
            throw new IllegalBlockingModeException();
        implConfigureBlocking(block);
        blocking = block;
    }
    return this;
}
```

非阻塞 socket 通常被认为是服务端使用的，因为它们使同时管理很多 socket 通道变得更容易。但是，在客户端使用一个或几个非阻塞模式的 socket 通道也是有益处的， 例如，借助非阻塞 socket 通道，GUI 程序可以专注于用户请求并且同时维护与一个或 多个服务器的会话。在很多程序上，非阻塞模式都是有用的。 

偶尔地，我们也会需要防止 socket 通道的阻塞模式被更改。API 中有一个 blockingLock( )方法，该方法会返回一个非透明的对象引用。返回的对象是通道实现 修改阻塞模式时内部使用的。只有拥有此对象的锁的线程才能更改通道的阻塞模式。 下面分别介绍这 3 个通道

#### 2.5.1 ServerSocketChannel

**ServerSocketChannel 是一个基于通道的 socket 监听器**。它同我们所熟悉的 java.net.ServerSocket 执行相同的任务，不过它增加了通道语义，因此**能够在非阻塞 模式下运行**。

由于 ServerSocketChannel 没有 bind()方法（**JDK1.8有bind方法**），因此有必要取出对等的 socket 并使用 它来绑定到一个端口以开始监听连接。我们也是使用对等 ServerSocket 的 API 来根 据需要设置其他的 socket 选项。

同 java.net.ServerSocket 一样，ServerSocketChannel 也有 **accept( )方法**。一旦创建了一个ServerSocketChannel 并用对等 socket 绑定了它，然后您就可以在其中一 个上调用 accept()。如果您选择在 ServerSocket 上调用 accept( )方法，那么它会同 任何其他的 ServerSocket 表现一样的行为：总是阻塞并返回一个 java.net.Socket 对 象。如果您选择在 ServerSocketChannel 上调用 accept( )方法则**会返回 SocketChannel 类型的对象**，返回的对象能够在非阻塞模式下运行。

换句话说：
ServerSocketChannel 的 accept()方法会返回 SocketChannel 类型对象， SocketChannel 可以在非阻塞模式下运行。

其它 Socket 的 accept()方法会阻塞返回一个 Socket 对象。如果 ServerSocketChannel 以非阻塞模式被调用，当没有传入连接在等待时， ServerSocketChannel.accept( )会立即返回 null。正是这种检查连接而不阻塞的能力 实现了可伸缩性并降低了复杂性。可选择性也因此得到实现。我们可以使用一个选择 器实例来注册 ServerSocketChannel 对象以实现新连接到达时自动通知的功能。



以下代码演示了如何使用一个非阻塞的 accept( )方法：

```java
package nio.chapter02;

import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;

/**
 * @author miemiehoho
 * @date 2022/2/23 16:51
 */
public class ServerSocketChannelDemo {

    public static void main(String[] args) throws Exception {

        int port = 8888;// 端口号
        // 创建buffer
        // wrap方法把字节数组包装成ByteBuffer对象
        ByteBuffer byteBuffer = ByteBuffer.wrap("hello!".getBytes());

        // ServerSocketChannel
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();

        // 绑定，JDK1.8可以直接调用 bind()方法
        serverSocketChannel.bind(new InetSocketAddress(port));
//        serverSocketChannel.socket().bind(new InetSocketAddress(port));

        // 设置非阻塞模式
        serverSocketChannel.configureBlocking(false);

        // 监听是否有新的连接传入
        while (true) {
            System.out.println("waiting for connections");
            SocketChannel socketChannel = serverSocketChannel.accept();
            if (socketChannel == null) {
                System.out.println("null...");
                Thread.sleep(3000);
            } else {
                System.out.println("new connection:" + socketChannel.socket().getRemoteSocketAddress());
                byteBuffer.rewind();// 指针指向0
                socketChannel.write(byteBuffer);
                socketChannel.close();
            }
        }
    }
}
```

![image-20220223171136215](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202231711273.png)

**（1）打开 ServerSocketChannel** 

通过调用 ServerSocketChannel.open() 方法来打开 ServerSocketChannel. 

`ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();`

**（2）关闭 ServerSocketChannel** 

通过调用 ServerSocketChannel.close() 方法来关闭 ServerSocketChannel. 

`serverSocketChannel.close();`

**（3）监听新的连接**

通过 ServerSocketChannel.accept() 方法监听新进的连接。当 accept()方法返回时候,它返回一个包含新进来的连接的 SocketChannel。因此, accept()方法会一直阻塞（如果是阻塞模式的话） 到有新连接到达。

通常不会仅仅只监听一个连接,在while 循环中调用 accept()方法. 如下面的例子：

```java
// 监听是否有新的连接传入
while (true) {
    System.out.println("waiting for connections");
    SocketChannel socketChannel = serverSocketChannel.accept();
```

**（4）阻塞模式**

会在 SocketChannel sc = ssc.accept();这里阻塞住进程。

![image-20220223171553176](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202231715230.png)



#### 2.5.2 SocketChannel 

##### 1、SocketChannel 介绍

Java NIO 中的 ==**SocketChannel 是一个连接到 TCP 网络套接字的通道**。== 

A selectable channel for stream-oriented connecting sockets.

以上是 Java docs 中对于 SocketChannel 的描述：SocketChannel 是一种面向流连接 sockets 套接字的可选择通道。从这里可以看出：

- SocketChannel 是**用来连接 Socket 套接字**
- SocketChannel 主要用途**用来处理网络 I/O的通道**
- SocketChannel 是**基于 TCP连接传输**
- SocketChannel 实现了可选择通道，可以被**多路复用**

##### 2、SocketChannel 特征：

（1）对于已经存在的 socket 不能创建 SocketChannel 

（2）**SocketChannel 中提供的 open 接口创建的Channel 并没有进行网络级联，需要使 用 connect 接口连接到指定地址** 

（3）未进行连接的 SocketChannle 执行 I/O操作时，会抛出 NotYetConnectedException 

（4）**SocketChannel 支持两种 I/O模式：阻塞式和非阻塞式** 

（5）**SocketChannel 支持异步关闭**。如果 SocketChannel 在一个线程上 read 阻塞，另 一个线程对该 SocketChannel 调用 shutdownInput，则读阻塞的线程将返回-1 表示没有 读取任何数据；如果 SocketChannel 在一个线程上write 阻塞，另一个线程对该 SocketChannel 调用 shutdownWrite，则写阻塞的线程将抛出 AsynchronousCloseException 

（6）**SocketChannel 支持设定参数** ：

- SO_SNDBUF 套接字发送缓冲区大小 

- SO_RCVBUF 套接字接收缓冲区大小 

- **SO_KEEPALIVE 保活连接** 
- **O_REUSEADDR 复用地址** 
- SO_LINGER 有数据传输时延缓关闭Channel (只有在非阻塞模式下有用) 
- TCP_NODELAY 禁用Nagle 算法

##### 3、SocketChannel 的使用

**（1）创建 SocketChannel**

 方式一：

```java
// 创建socketchannel
SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("www.baidu.com", 80));
```

方式二：

```java
SocketChannel socketChannel = SocketChannel.open();
socketChannel.connect(new InetSocketAddress("www.baidu.com", 80));
```

直接使用有参 open api 或者使用无参 open api，但是在**无参 open 只是创建了一个 SocketChannel 对象，并没有进行实质的 tcp 连接**。

**（2）连接校验**

```java
socketChannel.isOpen(); // 测试SocketChannel是否为open状态 
socketChannel.isConnected(); //测试SocketChannel是否已经被连接 
socketChannel.isConnectionPending(); //测试SocketChannel是否正在进行 连接 
socketChannel.finishConnect(); //校验正在进行套接字连接的SocketChannel 是否已经完成连接
```

**（3）读写模式** 

前面提到 SocketChannel 支持阻塞和非阻塞两种模式：

```java
socketChannel.configureBlocking(false);
```

通过以上方法设置 SocketChannel 的读写模式。false 表示非阻塞，true表示阻塞。

**（4）读写**

```java
SocketChannel socketChannel = SocketChannel.open();
socketChannel.connect(new InetSocketAddress("www.baidu.com", 80));
ByteBuffer byteBuffer = ByteBuffer.allocate(16);
socketChannel.read(byteBuffer);
socketChannel.close();
System.out.println("read over");
```

以上为阻塞式读，当执行到 read，线程将阻塞，控制台将无法打印 read over

```java
SocketChannel socketChannel = SocketChannel.open();
socketChannel.connect(new InetSocketAddress("www.baidu.com", 80));
socketChannel.configureBlocking(false);
ByteBuffer byteBuffer = ByteBuffer.allocate(16);
socketChannel.read(byteBuffer);
socketChannel.close();
System.out.println("read over");
```

以上为非阻塞读，控制台将打印 read over 

读写都是面向缓冲区，这个读写方式与前文中的 FileChannel 相同。

**（5）设置和获取参数**

```java
socketChannel.setOption(StandardSocketOptions.SO_KEEPALIVE, Boolean.TRUE)
        .setOption(StandardSocketOptions.TCP_NODELAY, Boolean.TRUE);
```

通过 `setOptions` 方法可以设置 socket 套接字的相关参数

```java
socketChannel.getOption(StandardSocketOptions.SO_KEEPALIVE);
socketChannel.getOption(StandardSocketOptions.SO_RCVBUF);
```

可以通过 `getOption` 获取相关参数的值。如默认的接收缓冲区大小是 8192byte。 

**SocketChannel 还支持多路复用**，多路复用在后续内容中会介绍到。

#### 2.5.3 DatagramChannel

正如 SocketChannel 对应 Socket，ServerSocketChannel 对应 ServerSocket，每 一个 DatagramChannel 对象也有一个关联的DatagramSocket 对象。正如 SocketChannel 模拟连接导向的流协议（如 TCP/IP），DatagramChannel 则模拟包 导向的无连接协议（如UDP/IP）。**DatagramChannel 是无连接的，每个数据报 （datagram）都是一个自包含的实体，拥有它自己的目的地址及不依赖其他数据报的 数据负载**。与面向流的的 socket 不同，**DatagramChannel 可以发送单独的数据报给 不同的目的地址**。同样，**DatagramChannel 对象也可以接收来自任意地址的数据 包。每个到达的数据报都含有关于它来自何处的信息（源地址）**



##### 1、打开DatagramChannel

```java
DatagramChannel datagramChannel = DatagramChannel.open();
datagramChannel.socket().bind(new InetSocketAddress(10086));
```

此例子是打开 10086 端口接收UDP数据包

##### 2、接收数据

通过 receive()接收UDP包

```java
ByteBuffer receiveBuffer = ByteBuffer.allocate(64);
receiveBuffer.clear();
SocketAddress receiveAddr = datagramChannel.receive(receiveBuffer);
```

SocketAddress 可以获得发包的 ip、端口等信息，用 toString 查看，格式如下

 /127.0.0.1:57126

##### 3、发送数据

通过 send()发送UDP包

```java
DatagramChannel datagramChannel = DatagramChannel.open();
ByteBuffer sendBuffer = ByteBuffer.wrap("hello channel!".getBytes());
datagramChannel.send(sendBuffer, new InetSocketAddress("127.0.0.1", 10086));
```

##### 4、连接

UDP不存在真正意义上的连接，这里的连接是向特定服务地址用 read 和write 接收 发送数据包。

```java
DatagramChannel datagramChannel = DatagramChannel.open();
datagramChannel.connect(new InetSocketAddress("127.0.0.1", 10086));
ByteBuffer buffer = ByteBuffer.wrap("hello!".getBytes());
int read = datagramChannel.read(buffer);
datagramChannel.write(buffer);
```

read()和write()只有在 connect()后才能使用，不然会抛 NotYetConnectedException 异常。

用 read()接收时，如果没有接收到包，会抛 PortUnreachableException 异常。

##### 5、DatagramChannel 示例

客户端发送，服务端接收的例子

```java
package nio.chapter02;

import org.junit.Test;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.net.SocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.DatagramChannel;
import java.nio.charset.Charset;

/**
 * @author miemiehoho
 * @date 2022/2/23 18:07
 */
public class DatagramChannelDemo {

    /**
     * 发包的 datagram
     */
    @Test
    public void sendDatagram() {
        try (DatagramChannel sendChannel = DatagramChannel.open()) {
            InetSocketAddress address = new InetSocketAddress("127.0.0.1", 9999);
            while (true) {
                sendChannel.send(ByteBuffer.wrap("这是发送的数据包".getBytes("UTF-8")), address);
                System.out.println("发送端发送数据包");
                Thread.sleep(3000);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 收报端
     */
    @Test
    public void receiveDatagram() {
        try (DatagramChannel receiveChannel = DatagramChannel.open()) {
            InetSocketAddress address = new InetSocketAddress(9999);
            receiveChannel.bind(address);
            ByteBuffer buffer = ByteBuffer.allocate(112);
            while (true) {
                buffer.clear();
                SocketAddress socketAddress = receiveChannel.receive(buffer);
                buffer.flip();
                System.out.print(socketAddress + " :");
                System.out.println(Charset.forName("UTF-8").decode(buffer));

            }
        } catch (Exception e) {
            e.printStackTrace();
        }

    }


    /**
     * 收发包
     */
    @Test
    public void testConnect() {
        try (DatagramChannel channel = DatagramChannel.open()) {
            channel.bind(new InetSocketAddress(9999));
            channel.connect(new InetSocketAddress("127.0.0.1", 9999));
            ByteBuffer receiveBuffer = ByteBuffer.allocate(123);
            while (true) {
                channel.write(ByteBuffer.wrap("testConnet 发包".getBytes("UTF-8")));

                receiveBuffer.clear();
                SocketAddress socketAddress = channel.receive(receiveBuffer);
                receiveBuffer.flip();
                System.out.print(socketAddress + "testConnect收报: ");
                System.out.println(Charset.forName("UTF-8").decode(receiveBuffer));
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
```



### 2.5 Scatter/Gather

Java NIO 开始支持 scatter/gather，scatter/gather 用于描述从Channel 中读取或 者写入到Channel 的操作。



**分散（scatter）**从Channel 中读取是指在读操作时将读取的数据写入多个 buffer 中。因此，Channel 将从 Channel 中读取的数据“分散（scatter）”到多个Buffer 中。

**聚集（gather）**写入 Channel 是指在写操作时将多个 buffer 的数据写入同一个 Channel，因此，Channel 将多个 Buffer 中的数据“聚集（gather）”后发送到 Channel。

scatter / gather 经常用于需要将传输的数据分开处理的场合，例如传输一个由消息头 和消息体组成的消息，你可能会将消息体和消息头分散到不同的 buffer 中，这样你可 以方便的处理消息头和消息体。

#### 2.5.1 Scattering Reads

 Scattering Reads 是指数据从一个 channel 读取到多个buffer 中。如下图描述：

![image-20220223202128905](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202232021966.png)

```java
ByteBuffer header = ByteBuffer.allocate(111);
ByteBuffer body = ByteBuffer.allocate(9999);
ByteBuffer[] bufferArray = {header,body};
channel.read(bufferArray);
```

注意 buffer 首先被插入到数组，然后再将数组作为 channel.read() 的输入参数。 read()方法按照 buffer 在数组中的顺序将从 channel 中读取的数据写入到 buffer，当 一个 buffer 被写满后，channel 紧接着向另一个buffer 中写。

Scattering Reads 在移动下一个buffer 前，必须填满当前的 buffer，这也意味着它 不适用于动态消息(译者注：消息大小不固定)。换句话说，**如果存在消息头和消息体， 消息头必须完成填充（例如 128byte），Scattering Reads 才能正常工作**。



#### 2.5.2 Gathering Writes

Gathering Writes 是指数据从多个 buffer 写入到同一个 channel。如下图描述：

![image-20220223202553342](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202232025393.png)

```java
ByteBuffer header = ByteBuffer.allocate(111);
ByteBuffer body = ByteBuffer.allocate(9999);
ByteBuffer[] bufferArray = {header,body};
channel.write(bufferArray);
```

buffers 数组是write()方法的入参，write()方法会按照 buffer 在数组中的顺序，将数据写入到 channel，注意只有 position 和 limit 之间的数据才会被写入。因此，如果 一个 buffer 的容量为 128byte，但是仅仅包含 58byte 的数据，那么这 58byte 的数 据将被写入到 channel 中。因此**与 Scattering Reads 相反，Gathering Writes 能较 好的处理动态消息。**



## 第 3 章 Java NIO（Buffer）

### 3.1 Buffer 简介

Java NIO 中的 Buffer 用于和 NIO通道进行交互。数据是从通道读入缓冲区，从缓冲 区写入到通道中的。

![image-20220223204103528](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202232041592.png)

**缓冲区本质上是一块可以写入数据，然后可以从中读取数据的内存**。这块内存被包装 成 NIO Buffer 对象，并提供了一组方法，用来方便的访问该块内存。缓冲区实际上是 一个容器对象，更直接的说，**其实就是一个数组，在 NIO库中，所有数据都是用缓冲 区处理的**。在读取数据时，它是直接读到缓冲区中的； 在写入数据时，它也是写入到 缓冲区中的；**任何时候访问 NIO 中的数据，都是将它放到缓冲区中。而在面向流 I/O 系统中，所有数据都是直接写入或者直接将数据读取到 Stream对象中**。

在 NIO中，所有的缓冲区类型都继承于抽象类Buffer，最常用的就是ByteBuffer， 对于 Java 中的基本类型，基本都有一个具体 Buffer 类型与之相对应，它们之间的继 承关系如下图所示：![image-20220223204641351](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202232046409.png)



### 3.2 Buffer 的基本用法

#### 1、使用Buffer 读写数据，一般遵循以下四个步骤： 

（1）写入数据到 Buffer 

（2）调用 flip()方法 

（3）从Buffer 中读取数据 

（4）调用 clear()方法或者 compact()方法

当向 buffer 写入数据时，buffer 会记录下写了多少数据。一旦**要读取数据，需要通过 flip()方法将 Buffer 从写模式切换到读模式。**在读模式下，可以读取之前写入到 buffer 的所有数据。一旦读完了所有的数据，就需要清空缓冲区，让它可以再次被写入。有 两种方式能清空缓冲区：调用 clear()或 compact()方法。**clear()方法会清空整个缓冲 区。compact()方法只会清除已经读过的数据。任何未读的数据都被移到缓冲区的起 始处，新写入的数据将放到缓冲区未读数据的后面。**

#### 2、使用 Buffer 的例子

```java
public static void main(String[] args) throws Exception {
    RandomAccessFile accessFile = new RandomAccessFile("D:\\13686\\OneDrive - 365word\\miemiehoho\\Project\\practice\\src\\main\\java\\nio\\chapter03\\1.txt", "rw");
    FileChannel inChannel = accessFile.getChannel();

    // 创建buffer并指定容量 128bytes
    ByteBuffer buffer = ByteBuffer.allocate(128);
    int read = inChannel.read(buffer);
    while (read != -1) {
        buffer.flip();// 写模式转换为读模式
        while (buffer.hasRemaining()) {
            System.out.print((char) buffer.get());// 一次读1byte
        }
        buffer.clear();// 读转写模式
        read = inChannel.read(buffer);
    }
    accessFile.close();
    inChannel.close();
}
```

#### 3、使用 IntBuffer 的例子

```java
public static void main(String[] args) {
    // 分配新的int缓冲区，参数为缓冲区容量
    // 新缓冲区的当前位置将为零，其界限(限制位置)将为其容量。
    // 它将具有一个底层实现数组，其数组偏移量将为零。
    IntBuffer intBuffer = IntBuffer.allocate(8);
    for (int i = 0; i < intBuffer.capacity(); i++) {
        // 写入指定整数到缓冲区的当前位置，当前位置递增
        intBuffer.put(2 * (i + 1));
    }

    // 重设此缓冲区，将限制设置为当前位置，然后将当前位置置为0
    intBuffer.flip();

    // 查看在当前位置和限制位置之间是否有元素
    while (intBuffer.hasRemaining()) {
        // 读取缓冲区当前位置的整数，然后当前位置递增
        int i = intBuffer.get();
        System.out.print(i + " ");
    }

}
```

### 3.3 Buffer 的 capacity、position 和 limit

为了理解Buffer 的工作原理，需要熟悉它的三个属性： 

- Capacity
- Position 
- limit

position 和 limit 的含义取决于Buffer 处在读模式还是写模式。不管 Buffer 处在什么 模式，capacity 的含义总是一样的。 

这里有一个关于 capacity，position 和 limit 在读写模式中的说明

![image-20220223211254016](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202232112105.png)

#### （1）capacity

作为一个内存块，Buffer 有一个**固定的大小值，也叫“capacity”.**你只能往里写 capacity 个 byte、long，char 等类型。一旦Buffer 满了，需要将其清空（通过读数 据或者清除数据）才能继续写数据往里写数据。

#### （2）position

**1）写数据到 Buffer 中时**，**position 表示写入数据的当前位置**，position 的初始值为 0。当一个 byte、long等数据写到 Buffer 后， position 会向下移动到下一个可插入 数据的Buffer 单元。position 最大可为 capacity – 1（因为 position 的初始值为 0）.

**2）读数据到 Buffer 中时**，**position 表示读入数据的当前位置**，如position=2 时表 示已开始读入了 3 个 byte，或从第 3 个 byte 开始读取。通过ByteBuffer.flip()切换 到读模式时 position 会被重置为 0，当Buffer 从 position 读入数据后，position 会 下移到下一个可读入的数据 Buffer 单元。

#### （3）limit

**1）写数据时**，**limit 表示可对 Buffer 最多写入多少个数据**。写模式下，limit 等于 Buffer 的 capacity。

 **2）读数据时**，**limit 表示 Buffer 里有多少可读数据（not null 的数据）**，因此能读到 之前写入的所有数据（limit 被设置成已写数据的数量，这个值**在写模式下就是 position**）。

### 3.4 Buffer 的类型

Java NIO 有以下 Buffer 类型 

- ByteBuffer 
- MappedByteBuffer 
- CharBuffer 
- DoubleBuffer 
- FloatBuffer

- IntBuffer 
- LongBuffer 
- ShortBuffer

这些 Buffer 类型代表了不同的数据类型。换句话说，就是可以通过 char，short， int，long，float 或 double 类型来操作缓冲区中的字节。

### 3.5 Buffer 分配和读写数据

#### 1、Buffer 分配

要想获得一个Buffer 对象首先要进行分配。 每一个 Buffer 类都有一个 allocate 方 法。

下面是一个分配 48 字节 capacity 的 ByteBuffer 的例子。 

`ByteBuffer buf = ByteBuffer.allocate(48);`

这是分配一个可存储 1024 个字符的CharBuffer： 

`CharBuffer buf = CharBuffer.allocate(1024);`

#### 2、向Buffer 中写数据

**写数据到Buffer 有两种方式：** 

（1）从Channel 写到Buffer。 

（2）通过 Buffer 的 put()方法写到Buffer 里。 

**从 Channel 写到Buffer 的例子** 

```java
int bytesRead = inChannel.read(buf); //read into buffer.
```

**通过 put 方法写Buffer 的例子：**

```java
buf.put(127);
```

put 方法有很多版本，允许你以不同的方式把数据写入到 Buffer 中。例如， 写到一个 指定的位置，或者把一个字节数组写入到Buffer

#### 3、flip()方法

**flip 方法将 Buffer 从写模式切换到读模式。**调用 flip()方法会将 position 设回 0，并 将 limit 设置成之前 position 的值。换句话说，position 现在用于标记读的位置， limit 表示之前写进了多少个 byte、char 等 （现在能读取多少个 byte、char 等）。

#### 4、 从Buffer 中读取数据

**从Buffer 中读取数据有两种方式：**

 （1）从Buffer 读取数据到 Channel。 

（2）使用 get()方法从 Buffer 中读取数据。

**从Buffer 读取数据到 Channel 的例子：** 

```java
//read from buffer into channel. 

int bytesWritten = inChannel.write(buf);
```

**使用 get()方法从Buffer 中读取数据的例子** 

```java
byte aByte = buf.get();
```

get 方法有很多版本，允许你以不同的方式从 Buffer 中读取数据。例如，从指定 position 读取，或者从Buffer 中读取数据到字节数组。

### 3.6 Buffer 几个方法

#### 1、rewind()方法

Buffer.rewind()将 position 设回 0，所以你可以重读 Buffer 中的所有数据。limit 保 持不变，仍然表示能从Buffer 中读取多少个元素（byte、char 等）。

#### 2、clear()与 compact()方法

一旦读完Buffer 中的数据，需要让 Buffer 准备好再次被写入。可以通过 clear()或 compact()方法来完成。

如果调用的是 clear()方法，position 将被设回 0，limit 被设置成 capacity 的值。换 句话说，Buffer 被清空了。Buffer 中的数据并未清除，只是这些标记告诉我们可以从 哪里开始往 Buffer 里写数据。

如果 Buffer 中有一些未读的数据，调用 clear()方法，数据将“被遗忘”，意味着不再 有任何标记会告诉你哪些数据被读过，哪些还没有。

如果 Buffer 中仍有未读的数据，且后续还需要这些数据，但是此时想要先先写些数 据，那么使用 compact()方法。 

compact()方法将所有未读的数据拷贝到Buffer 起始处。然后将position 设到最后一 个未读元素正后面。limit 属性依然像 clear()方法一样，设置成 capacity。现在 Buffer 准备好写数据了，但是不会覆盖未读的数据。

#### 3、mark()与 reset()方法（类似java中的事务）

通过调用Buffer.mark()方法，可以标记Buffer 中的一个特定 position。之后可以通 过调用Buffer.reset()方法恢复到这个 position。例如：

```java
buffer.mark(); //call buffer.get() a couple of times, e.g. during parsing. 
buffer.reset(); //set position back to mark.
```

### 3.7 缓冲区操作 

#### 1、缓冲区分片

在 NIO中，除了可以分配或者包装一个缓冲区对象外，还可以根据现有的缓冲区对象 来创建一个子缓冲区，即在现有缓冲区上切出一片来作为一个新的缓冲区，但**现有的 缓冲区与创建的子缓冲区在底层数组层面上是数据共享的**，也就是说，子缓冲区相当 于是现有缓冲区的一个视图窗口。调用 slice()方法可以创建一个子缓冲区。



切片是调用slice方法时原始缓冲区的position到limit索引之间的数据

```java
@Test
public void bf1() {
    ByteBuffer buffer = ByteBuffer.allocate(10);
    // 向缓冲区写入 0-9
    for (int i = 0; i < buffer.capacity(); i++) {
        buffer.put((byte) i);
    }
    // 创建子缓冲区
    buffer.position(3);// 设置position位置
    buffer.limit(7);// 设置limit位置
    ByteBuffer slice = buffer.slice();

    // 改变子缓冲区内容
    for (int i = 0; i < slice.capacity(); i++) {
        byte var = slice.get(i);
        var *= 10;
        slice.put(i, var);
    }

    buffer.position(0);
    buffer.limit(buffer.capacity());

    while (buffer.hasRemaining()) {
        System.out.println(buffer.get());
    }

}
```

#### 2、只读缓冲区

只读缓冲区非常简单，可以读取它们，但是不能向它们写入数据。可以通过调用缓冲 区的 asReadOnlyBuffer()方法，将任何常规缓冲区转 换为只读缓冲区，这个方法返 回一个与原缓冲区完全相同的缓冲区，并与原缓冲区共享数据，只不过它是只读的。 如果原缓冲区的内容发生了变化，只读缓冲区的内容也随之发生变化：



注意要手动设置只读缓冲区的postion和limit

```java
@Test
public void bf2() {
    ByteBuffer buffer = ByteBuffer.allocate(10);
    // 向缓冲区写入 0-9
    for (int i = 0; i < buffer.capacity(); i++) {
        buffer.put((byte) i);
    }

    // 创建只读缓冲区
    ByteBuffer readOnlyBuffer = buffer.asReadOnlyBuffer();

    for (int i = 0; i < buffer.capacity(); i++) {
        byte b = buffer.get(i);
        b *= 10;
        buffer.put(i, b);
    }

    readOnlyBuffer.position(0);
    readOnlyBuffer.limit(buffer.capacity());
    while (readOnlyBuffer.hasRemaining()) {
        System.out.println(readOnlyBuffer.get());
    }
}
```

#### 3、直接缓冲区

直接缓冲区是为**加快 I/O速度**，使用一种特殊方式为其分配内存的缓冲区，JDK文档 中的描述为：给定一个直接字节缓冲区，Java 虚拟机将尽最大努力直接对它执行本机 I/O操作。也就是说，**它会在每一次调用底层操作系统的本机 I/O操作之前(或之后)， 尝试避免将缓冲区的内容拷贝到一个中间缓冲区中 或者从一个中间缓冲区中拷贝数 据。**要分配直接缓冲区，需要调用 `allocateDirect()`方法，而不是 allocate()方法，使用方式与普通缓冲区并无区别。



allocateDirect调用Unsafe直接向内存申请缓冲区，而allocate调用JVM内存

拷贝文件示例：

```java
@Test
public void bf3() {
    String dir1 = "D:\\13686\\OneDrive - 365word\\miemiehoho\\Project\\practice\\src\\main\\java\\nio\\chapter03\\1.txt";
    String dir2 = "D:\\13686\\OneDrive - 365word\\miemiehoho\\Project\\practice\\src\\main\\java\\nio\\chapter03\\2.txt";
    try (
            FileInputStream inputStream = new FileInputStream(dir1);
            FileOutputStream outputStream = new FileOutputStream(dir2);
            FileChannel inChannel = inputStream.getChannel();
            FileChannel outChannel = outputStream.getChannel();
    ) {
        // 创建直接缓冲区
        ByteBuffer buffer = ByteBuffer.allocateDirect(1024);
        while (true) {
            buffer.clear();
            int read = inChannel.read(buffer);
            if (read == -1) {
                break;
            }
            buffer.flip();
            outChannel.write(buffer);
        }
    } catch (Exception e) {
        e.printStackTrace();
    }
    System.out.println("over!");
}
```

#### 4、内存映射文件 I/O

内存映射文件 I/O是一种读和写文件数据的方法，它可以比常规的基于流或者基于通 道的 I/O快的多。内存映射文件 I/O是通过使文件中的数据出现为 内存数组的内容来 完成的，这其初听起来似乎不过就是将整个文件读到内存中，但是事实上并不是这 样。一般来说，**只有文件中实际读取或者写入的部分才会映射到内存中**。

示例代码：

```java
@Test
public void bf4() {
    int start = 0;
    int size = 1024;

    try (
            RandomAccessFile accessFile = new RandomAccessFile("D:\\13686\\OneDrive - 365word\\miemiehoho\\Project\\practice\\src\\main\\java\\nio\\chapter03\\3.txt", "rw");
            FileChannel channel = accessFile.getChannel();
    ) {
        MappedByteBuffer mappedByteBuffer = channel.map(FileChannel.MapMode.READ_WRITE, start, size);
        mappedByteBuffer.put(0, (byte) 93);
        mappedByteBuffer.put(1023, (byte) 145);
    } catch (Exception e) {
        e.printStackTrace();
    }
    System.out.println("over!");

}
```

MappedByteBuffer的唯一子类为DirectByteBuffer，使用的直接内存，所以会快一些

HeapedByteBuffer是ByteBuffer的一个实现，是基于JVM内存的缓冲区，和DirectByteBuffer相对



## 第 4 章 Java NIO（Selector）

### 4.1 Selector 简介

#### 1、Selector 和 Channel 关系

Selector 一般称 为选择器 ，也可以翻译为 多路复用器 。它是 Java NIO 核心组件中 的一个，**用于检查一个或多个 NIO Channel（通道）的状态是否处于可读、可写**。如 此可以实现单线程管理多个 channels,也就是可以管理多个网络链接。

 ![image-20220223225331374](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202232253465.png)

使用 Selector 的好处在于： 使用更少的线程来就可以来处理通道了， 相比使用多个 线程，避免了线程上下文切换带来的开销。

#### 2、可选择通道(SelectableChannel)

（1）**不是所有的Channel 都可以被 Selector 复用的**。比方说，**FileChannel 就不能 被选择器复用**。判断一个 Channel 能被 Selector 复用，有一个前提：判断他是否继承了一个抽象类 SelectableChannel。**如果继承了 SelectableChannel，则可以被复用，否则不能。**

（2）**SelectableChannel** 类提供了实现通道的可选择性所需要的公共方法。**它是所有 支持就绪检查的通道类的父类**。所有 socket 通道，都继承了 SelectableChannel 类 都是可选择的，包括从管道(Pipe)对象的中获得的通道。而 FileChannel 类，没有继 承 SelectableChannel，因此是不是可选通道。

（3）**一个通道可以被注册到多个选择器上**，但对每个选择器而言只能被注册一次。通 道和选择器之间的关系，使用注册的方式完成。SelectableChannel 可以被注册到 Selector 对象上，在注册的时候，需要指定通道的哪些操作，是 Selector 感兴趣的。

![image-20220223225700750](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202232257856.png)

#### 3、Channel 注册到 Selector

（1）使用 Channel.register（Selector sel，int ops）方法，将一个通道注册到一个 选择器时。第一个参数，指定通道要注册的选择器。第二个参数指定选择器需要查询 的通道操作。
（2）可以供选择器查询的通道操作，从类型来分，包括以下四种：

- 可读 : SelectionKey.OP_READ
- 可写 : SelectionKey.OP_WRITE
- 连接 : SelectionKey.OP_CONNECT 
- 接收 : SelectionKey.OP_ACCEPT

如果 Selector 对通道的多操作类型感兴趣，可以用“位或”操作符来实现： 

比如：

```java
int key = SelectionKey.OP_READ | SelectionKey.OP_WRITE ;
```

（3）**选择器查询的不是通道的操作，而是通道的某个操作的一种就绪状态**。什么是操 作的就绪状态？一旦通道具备完成某个操作的条件，表示该通道的某个操作已经就 绪，就可以被 Selector 查询到，程序可以对通道进行对应的操作。比方说，某个 SocketChannel 通道可以连接到一个服务器，则处于“连接就绪” (OP_CONNECT)。

再比方说，一个 ServerSocketChannel 服务器通道准备好接收新 进入的连接，则处于“接收就绪”（OP_ACCEPT）状态。还比方说，一个有数据可读 的通道，可以说是“读就绪”(OP_READ)。一个等待写数据的通道可以说是“写就 绪”(OP_WRITE)。

#### 4、选择键(SelectionKey)

（1）Channel 注册到后，并且一旦通道处于某种就绪的状态，就可以被选择器查询 到。这个工作，使用选择器 Selector 的 select（）方法完成。select 方法的作用，对 感兴趣的通道操作，进行就绪状态的查询。

（2）Selector 可以不断的查询Channel 中发生的操作的就绪状态。并且挑选感兴趣 的操作就绪状态。一旦通道有操作的就绪状态达成，并且是 Selector 感兴趣的操作， 就会被 Selector 选中，放入选择键集合中。

（3）一个选择键，首先是包含了注册在 Selector 的通道操作的类型，比方说 SelectionKey.OP_READ。也包含了特定的通道与特定的选择器之间的注册关系。
开发应用程序是，选择键是编程的关键。**NIO的编程，就是根据对应的选择键，进行 不同的业务逻辑处理。**

（4）选择键的概念，和事件的概念比较相似。**一个选择键类似监听器模式里边的一个 事件**。由于 Selector 不是事件触发的模式，而是主动去查询的模式，所以不叫事件 Event，而是叫 SelectionKey 选择键。



### 4.2 Selector 的使用方法

#### 1、Selector 的创建

通过调用 Selector.open()方法创建一个 Selector 对象，如下：

```java
Selector selector = Selector.open();
```

#### 2、注册Channel 到 Selector

要实现 Selector 管理 Channel，需要将 channel 注册到相应的 Selector 上

```java
// 获取selector选择器
Selector selector = Selector.open();

// 获取通道
ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();

// 设置为非阻塞
serverSocketChannel.configureBlocking(false);

// 绑定连接
serverSocketChannel.bind(new InetSocketAddress(9999));

// 将通道注册到选择器上，并制定监听事件为：“接受”事件
serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
```

上面通过调用通道的 register()方法会将它注册到一个选择器上。

首先需要注意的是：

（1）与 Selector 一起使用时，**Channel 必须处于非阻塞模式下**，否则将抛出异常 IllegalBlockingModeException。这意味着，FileChannel 不能与 Selector 一起使用，因 为 FileChannel 不能切换到非阻塞模式，而套接字相关的所有的通道都可以。

（2）**一个通道，并没有一定要支持所有的四种操作**。比如服务器通道 ServerSocketChannel 支持 Accept 接受操作，而 SocketChannel 客户端通道则不支 持。**可以通过通道上的 validOps()方法，来获取特定通道下所有支持的操作集合。**



#### 3、轮询查询就绪操作

（1）通过 Selector 的 **select（）方法，可以查询出已经就绪的通道操作**，这些就绪的 状态集合，包存在一个元素是 SelectionKey对象的 Set 集合中。 

（2）下面是 Selector 几个重载的查询 select()方法：

- select():阻塞到至少有一个通道在你注册的事件上就绪了。 
- select(long timeout)：和 select()一样，但最长阻塞事件为 timeout 毫秒。 
- selectNow():非阻塞，只要有通道就绪就立刻返回。

select()方法返回的 int 值，表示有多少通道已经就绪，更准确的说，是自前一次 select 方法以来到这一次 select 方法之间的时间段上，有多少通道变成就绪状态。

例如：首次调用 select()方法，如果有一个通道变成就绪状态，返回了 1，若再次调用 select()方法，如果另一个通道就绪了，它会再次返回 1。如果对第一个就绪的 channel 没有做任何操作，现在就有两个就绪的通道，但在每次 select()方法调用之 间，只有一个通道就绪了。

一旦调用 select()方法，并且返回值不为 0 时，在 Selector 中有一个 selectedKeys()方 法，用来访问已选择键集合，迭代集合的每一个选择键元素，根据就绪操作的类型， 完成对应的操作：

```java
// 查询已就绪通道
Set<SelectionKey> selectionKeys = selector.selectedKeys();
// 遍历集合
Iterator<SelectionKey> iterator = selectionKeys.iterator();
while (iterator.hasNext()) {
    SelectionKey key = iterator.next();
    // 判断key就绪状态
    if (key.isAcceptable()) {

    } else if (key.isConnectable()) {

    } else if (key.isReadable()) {

    } else if (key.isWritable()) {

    }
    iterator.remove();
}
```

这里remove的目的是 将事件清除掉，这样代表事件被处理了，所以下次select的时候不会又选出来这个事件

#### 5、停止选择的方法

选择器执行选择的过程，系统底层会依次询问每个通道是否已经就绪，这个过程可能 会造成调用线程进入阻塞状态,那么我们有以下三种方式可以唤醒在 select（）方法中 阻塞的线程。

wakeup()方法 ：

通过调用 Selector 对象的**wakeup（）方法让处在阻塞状态的 select()方法立刻返回**

该方法使得选择器上的第一个还没有返回的选择操作立即返回。如果当前没有进行中 的选择操作，那么下一次对 select()方法的一次调用将立即返回。 

close()方法 ：通过 close（）方法关闭 Selector

 该方法使得任何一个在选择操作中阻塞的线程都被唤醒（类似wakeup（）），同时 使得注册到该 Selector 的所有Channel 被注销，所有的键将被取消，但是 Channel 本身并不会关闭。



### 4.3 示例代码 

#### 1、服务端代码

```java
/**
 * 服务端
 */
@Test
public void serverDemo() throws Exception {
    // 1 获取服务端通道
    ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
    // 2 切换为非阻塞模式（与 Selector 一起使用时，Channel 必须处于非阻塞模式下）
    serverSocketChannel.configureBlocking(false);
    // 3 创建buffer
    ByteBuffer writeBuffer = ByteBuffer.allocate(512);
    // 4 绑定端口号
    serverSocketChannel.bind(new InetSocketAddress(8081));
    // 5 获取selector选择器
    Selector selector = Selector.open();
    // 6 通道注册到选择器，进行监听
    serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
    // 7 选择器轮询，进行后续操作
    while (selector.select() > 0) {
        // 获取就绪的通道集合
        Set<SelectionKey> selectionKeys = selector.selectedKeys();
        // 遍历
        Iterator<SelectionKey> iterator = selectionKeys.iterator();
        while (iterator.hasNext()) {
            // 获取就绪操作
            SelectionKey next = iterator.next();
            // 判断什么操作
            if (next.isAcceptable()) {
                // 获取连接
                SocketChannel socketChannel = serverSocketChannel.accept();
                // 切换非阻塞模式
                socketChannel.configureBlocking(false);
                // 注册
                socketChannel.register(selector, SelectionKey.OP_READ);
            } else if (next.isReadable()) {
                SocketChannel socketChannel = (SocketChannel) next.channel();
                ByteBuffer readBuffer = ByteBuffer.allocate(512);
                // 读取数据
                int length = 0;
                while ((length = socketChannel.read(readBuffer)) > 0) {
                    readBuffer.flip();
                    System.out.println(new String(readBuffer.array(), 0, length));
                    readBuffer.clear();
                }
                socketChannel.read(readBuffer);
            } else if (next.isWritable()) {
                writeBuffer.rewind();
                SocketChannel socketChannel = (SocketChannel) next.channel();
                socketChannel.write(writeBuffer);
                next.interestOps(SelectionKey.OP_READ);
            }
            iterator.remove();
        }
    }
}
```

#### 2、客户端代码

```java
/**
 * 客户端
 */
@Test
public void clientDemo() throws Exception {
    // 1 获取通道，绑定主机和端口号
    SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 8081));
    // 2 切换到非阻塞模式
    socketChannel.configureBlocking(false);
    // 3 创建buffer
    ByteBuffer buffer = ByteBuffer.allocate(1024);
    // 4 写入buffer数据
    buffer.put((new Date().toString() + " 客户端发送数据-->").getBytes());
    // 5 buffer切换模式
    buffer.flip();
    // 6 写入通道
    socketChannel.write(buffer);
    // 7 关闭
    buffer.clear();
    socketChannel.close();

}
```

#### 3、完整Demo

```java
package nio.chapter04;

import org.junit.Test;

import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.Date;
import java.util.Iterator;
import java.util.Scanner;
import java.util.Set;

/**
 * @author miemiehoho
 * @date 2022/2/24 9:05
 */
public class SelectorDemo {

    // 客户都
    public static void main(String[] args) throws Exception {
        // 1 获取通道，绑定主机和端口号
        SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 8081));
        // 2 切换到非阻塞模式
        socketChannel.configureBlocking(false);
        // 3 创建buffer
        ByteBuffer buffer = ByteBuffer.allocate(1024);

        Scanner scanner = new Scanner(System.in);

        while (scanner.hasNext()) {
            String str = scanner.next();
            // 4 写入buffer数据
            buffer.put((new Date().toString() + " 客户端发送数据-->" + str).getBytes());
            // 5 buffer切换模式
            buffer.flip();
            // 6 写入通道
            socketChannel.write(buffer);

            buffer.clear();
        }
        // 7 关闭
        socketChannel.close();
    }

    /**
     * 服务端
     */
    @Test
    public void serverDemo() throws Exception {
        // 1 获取服务端通道
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        // 2 切换为非阻塞模式（与 Selector 一起使用时，Channel 必须处于非阻塞模式下）
        serverSocketChannel.configureBlocking(false);
        // 3 创建buffer
        ByteBuffer writeBuffer = ByteBuffer.allocate(512);
        // 4 绑定端口号
        serverSocketChannel.bind(new InetSocketAddress(8081));
        // 5 获取selector选择器
        Selector selector = Selector.open();
        // 6 通道注册到选择器，进行监听
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
        // 7 选择器轮询，进行后续操作
        while (selector.select() > 0) {
            // 获取就绪的通道集合
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            // 遍历
            Iterator<SelectionKey> iterator = selectionKeys.iterator();
            while (iterator.hasNext()) {
                // 获取就绪操作
                SelectionKey next = iterator.next();
                // 判断什么操作
                if (next.isAcceptable()) {
                    // 获取连接
                    SocketChannel socketChannel = serverSocketChannel.accept();
                    // 切换非阻塞模式
                    socketChannel.configureBlocking(false);
                    // 注册
                    socketChannel.register(selector, SelectionKey.OP_READ);
                } else if (next.isReadable()) {
                    SocketChannel socketChannel = (SocketChannel) next.channel();
                    ByteBuffer readBuffer = ByteBuffer.allocate(512);
                    // 读取数据
                    int length = 0;
                    while ((length = socketChannel.read(readBuffer)) > 0) {
                        readBuffer.flip();
                        System.out.println(new String(readBuffer.array(), 0, length));
                        readBuffer.clear();
                    }
                    socketChannel.read(readBuffer);
                } else if (next.isWritable()) {
                    writeBuffer.rewind();
                    SocketChannel socketChannel = (SocketChannel) next.channel();
                    socketChannel.write(writeBuffer);
                    next.interestOps(SelectionKey.OP_READ);
                }
                iterator.remove();
            }
        }
    }

}
```

#### 4、NIO 编程步骤总结

第一步：创建 Selector 选择器 

第二步：创建 ServerSocketChannel 通道，并绑定监听端口 

第三步：设置Channel 通道是非阻塞模式 

第四步：把Channel 注册到 Socketor 选择器上，监听连接事件

第五步：调用 Selector 的 select 方法（循环调用），监测通道的就绪状况 

第六步：调用 selectKeys 方法获取就绪 channel 集合 

第七步：遍历就绪 channel 集合，判断就绪事件类型，实现具体的业务操作 

第八步：根据业务，决定是否需要再次注册监听事件，重复执行第三步操作



## 第 5 章 Java NIO（Pipe 和 FileLock）

### 5.1 Pipe 

Java NIO 管道是 2 个**线程之间**的**单向数据连接**。Pipe 有一个 source 通道和一个 sink 通道。数据会被写到 sink 通道，从 source 通道读取。

![image-20220224111607731](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202241116814.png)

#### 1、创建管道

通过 Pipe.open()方法打开管道。 

```java
Pipe pipe = Pipe.open();
```

#### 2、写入管道

要向管道写数据，需要访问 sink 通道。：

```java
Pipe.SinkChannel sinkChannel = pipe.sink();
```

通过调用 SinkChannel 的write()方法，将数据写入 SinkChannel：

```java
String newData = "New String to write to file..." + System.currentTimeMillis();
ByteBuffer buf = ByteBuffer.allocate(48);
buf.clear();
buf.put(newData.getBytes());
buf.flip();
while (buf.hasRemaining()) {
    sinkChannel.write(buf);
}
```

#### 3、从管道读取数据

从读取管道的数据，需要访问 source 通道，像这样：

```java
Pipe.SourceChannel sourceChannel = pipe.source();
```

调用 source 通道的 read()方法来读取数据：

```java
ByteBuffer buf = ByteBuffer.allocate(48); 
int bytesRead = sourceChannel.read(buf);
```

read()方法返回的 int 值会告诉我们多少字节被读进了缓冲区。

#### 4、示例

```java
public static void main(String[] args) throws Exception {
    // 1 获取管道
    Pipe pipe = Pipe.open();
    // 2 获取sink通道
    Pipe.SinkChannel sinkChannel = pipe.sink();
    // 3 创建缓冲区
    ByteBuffer writeBuffer = ByteBuffer.allocate(1024);
    // 4 写入数据
    writeBuffer.put("写的数据".getBytes());
    writeBuffer.flip();
    while (writeBuffer.hasRemaining()) {
        sinkChannel.write(writeBuffer);
    }
    // 5 获取source通道
    Pipe.SourceChannel sourceChannel = pipe.source();
    // 6 创建缓冲区
    ByteBuffer readBuffer = ByteBuffer.allocate(1);
    // 7 读数据
    int length = 0;
    while ((length = sourceChannel.read(readBuffer)) > 0) {
        readBuffer.flip();
        System.out.print(new String(readBuffer.array(), 0, length));
        readBuffer.clear();
    }
    // 8 关闭通道
    sinkChannel.close();
    sourceChannel.close();
}
```

### 5.2 FileLock

####  1、FileLock 简介

文件锁在OS（操作系统）中很常见，如果多个程序同时访问、修改同一个文件，很容易因为文件 数据不同步而出现问题。给文件加一个锁，同一时间，只能有一个程序修改此文件， 或者程序都只能读此文件，这就解决了同步问题。

**文件锁是进程级别的，不是线程级别的。**文件锁可以解决多个进程并发访问、修改同 一个文件的问题，但不能解决多线程并发访问、修改同一文件的问题。**使用文件锁 时，同一进程内的多个线程，可以同时访问、修改此文件。**

文件锁是当前程序所属的 JVM实例持有的，一旦获取到文件锁（对文件加锁），要**调 用 release()**，或者关闭对应的 FileChannel 对象，或者**当前 JVM退出**，才会释放这 个锁。

一旦某个进程（比如说 JVM实例）对某个文件加锁，则在释放这个锁之前，此进程不 能再对此文件加锁，就是说 JVM实例在同一文件上的文件锁是不重叠的（**进程级别不 能重复在同一文件上获取锁**）。

#### 2、文件锁分类：

**排它锁：又叫独占锁**。对文件加排它锁后，该进程可以对此文件进行读写，该进程独 占此文件，其他进程不能读写此文件，直到该进程释放文件锁。

**共享锁：**某个进程对文件加共享锁，其他进程也可以访问此文件，但这些进程都只能 读此文件，不能写。线程是安全的。只要还有一个进程持有共享锁，此文件就只能 读，不能写。

#### 3、使用示例：

文件锁要通过 FileChannel 对象使用。

```java
// 创建filechannel对象，文件锁只能通过filechannel对象来使用
FileChannel fileChannel = new FileOutputStream("").getChannel();
// 对文件加锁
FileLock lock = fileChannel.lock();
// 对文件进行读写操作
// ...

// 释放锁
lock.release();
```

#### 4、获取文件锁方法

有 4 种获取文件锁的方法： 

```java
lock() //对整个文件加锁，默认为排它锁。 
```

```java
lock(long position, long size, booean shared) //自定义加锁方式。前 2 个参数指定要加锁的部分（可以只对此文件的部分内容加锁），第三个参数值指定是否是共享锁。
```

```java
tryLock() //对整个文件加锁，可以认为是lock方法的一个升级，默认为排它锁。
```

```java
tryLock(long position, long size, booean shared) //自定义加锁方式。如果指定为共享锁，则其它进程可读此文件，所有进程均不能写此文件，如果某进程 试图对此文件进行写操作，会抛出异常。
```

#### 5、lock 与 tryLock 的区别

**lock 是阻塞式的**，如果未获取到文件锁，会一直阻塞当前线程，直到获取文件锁 

tryLock 和 lock 的作用相同，只不过 **tryLock 是非阻塞式的**，tryLock 是尝试获取文 件锁，获取成功就返回锁对象，否则返回null，不会阻塞当前线程。

#### 6、FileLock 两个方法：

```java
boolean isShared() //此文件锁是否是共享锁

boolean isValid() //此文件锁是否还有效
```

在某些OS上，对某个文件加锁后，不能对此文件使用通道映射。

#### 7、完整例子

```java
package nio.chapter04;

import java.io.BufferedReader;
import java.io.FileReader;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;
import java.nio.channels.FileLock;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.nio.file.StandardOpenOption;

/**
 * @author miemiehoho
 * @date 2022/2/24 12:08
 */
public class FileLockDemo {
    public static void main(String[] args) throws Exception {
        String input = "hello!";
        System.out.println("input: " + input);
        ByteBuffer buffer = ByteBuffer.wrap(input.getBytes());

        String filePath = "D:\\13686\\OneDrive - 365word\\miemiehoho\\Project\\practice\\src\\main\\java\\nio\\chapter04\\1.txt";
        Path path = Paths.get(filePath);
        FileChannel fileChannel = FileChannel.open(path, StandardOpenOption.WRITE, StandardOpenOption.APPEND);
        fileChannel.position(fileChannel.size() - 1);// append，从最后开始追加
        // 加锁
        FileLock lock = fileChannel.lock();
        System.out.println("是否共享锁" + lock.isShared());
        fileChannel.write(buffer);
        lock.release();
        fileChannel.close();

        // 读文件
        readFile(filePath);

    }

    private static void readFile(String filePath) throws Exception {
        FileReader fileReader = new FileReader(filePath);
        BufferedReader bufferedReader = new BufferedReader(fileReader);
        String str = bufferedReader.readLine();
        System.out.println("读取内容：");
        while (str != null) {
            System.out.println(" " + str);
            str = bufferedReader.readLine();
        }
        fileReader.close();
        bufferedReader.close();
    }
}
```



##  第 6 章 Java NIO（其他）

### 6.1 Path 

#### 1、Path 简介

Java Path 接口是 Java NIO 更新的一部分，同 Java NIO一起已经包括在 Java6 和 Java7 中。Java Path 接口是在 Java7 中添加到 Java NIO的。Path 接口位于 java.nio.file 包中，所以 Path 接口的完全限定名称为 java.nio.file.Path。

 Java Path 实例表示文件系统中的路径。一个路径可以指向一个文件或一个目录。路径 可以是绝对路径，也可以是相对路径。绝对路径包含从文件系统的根目录到它指向的 文件或目录的完整路径。相对路径包含相对于其他路径的文件或目录的路径。 

**在许多方面，java.nio.file.Path 接口类似于 java.io.File 类**，但是有一些差别。不过， **在许多情况下，可以使用 Path 接口来替换 File 类的使用。**

#### 2、创建 Path 实例

使用 java.nio.file.Path 实例必须创建一个 Path 实例。可以使用 Paths 类 (java.nio.file.Paths)中的静态方法 Paths.get()来创建路径实例。 

示例代码:

```java
Path path = Paths.get("d:\\atguigu\\001.txt");
```

上述代码，可以理解为，Paths.get()方法是 Path 实例的工厂方法。

#### 3、创建绝对路径

（1）创建绝对路径，通过调用 Paths.get()方法，给定绝对路径文件作为参数来完 成。

示例代码：

```java
Path path = Paths.get("d:\\atguigu\\001.txt");
```

上述代码中，绝对路径是 d:\atguigu\001.txt。在 Java 字符串中， \是一个转义字 符，需要编写\\，告诉 Java 编译器在字符串中写入一个\字符。

（2）如果在 Linux、MacOS 等操作字体上，上面的绝对路径可能如下:

```java
Path path = Paths.get("/home/jakobjenkov/myfile.txt");
```

绝对路径现在为/home/jakobjenkov/myfile.txt.

（3）如果在Windows 机器上使用了从/开始的路径，那么路径将被解释为相对于当 前驱动器。

#### 4、创建相对路径

Java NIO Path 类也可以用于处理相对路径。您可以使用 Paths.get(basePath, relativePath)方法创建一个相对路径。

示例代码:

```java
// 代码1
Path projects = Paths.get("d:\\atguigu", "projects");

// 代码2
Path file = Paths.get("d:\\atguigu", "projects\\002.txt");
```

代码 1 创建了一个 Java Path 的实例，指向路径(目录):d:\atguigu\projects

代码 2 创建了一个 Path 的实例，指向路径(文件):d:\atguigu\projects\002.txt

#### 5、Path.normalize()

Path 接口的 normalize()方法可以使路径标准化。标准化意味着它将移除所有在路径 字符串的中间的.和..代码，并解析路径字符串所引用的路径。

Path.normalize()示例:

```java
String orginalPath = "d:\\atguigu\\projects\\..\\yygh-project";

Path path1 = Paths.get(orginalPath);
System.out.println("path1= " + path1);

Path path2 = path1.normalize();
System.out.println("path2= " + path2);
```

**输出结果：标准化的路径不包含 projects\..部分**

![image-20220224144607351](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202241446435.png)

### 6.2 Files

Java NIO Files 类(java.nio.file.Files)提供了几种操作文件系统中的文件的方法。以下 内容介绍 Java NIO Files 最常用的一些方法。java.nio.file.Files 类与 java.nio.file.Path 实例一起工作，因此在学习 Files 类之前，需要先了解 Path 类。

#### 1、Files.createDirectory()

Files.createDirectory()方法，用于根据 Path 实例创建一个新目录 

递归创建目录用 Files.createDirectories() 方法

示例：

```java
Path path = Paths.get("d:\\sgg");
try {
    Path newDir = Files.createDirectory(path);
} catch (FileAlreadyExistsException e) {
    // 目录已经存在
} catch (IOException e) {
    // 其他异常
    e.printStackTrace();
}
```

第一行创建表示要创建的目录的 Path 实例。在 try-catch 块中，用路径作为参数调用 Files.createDirectory()方法。如果创建目录成功，将返回一个 Path 实例，该实例指 向新创建的路径。

如果该目录已经存在，则是抛出一个 java.nio.file.FileAlreadyExistsException。如果 出现其他错误，可能会抛出 IOException。例如，如果想要的新目录的父目录不存 在，则可能会抛出 IOException。

#### 2、Files.copy()

##### （1）Files.copy()方法从一个路径拷贝一个文件到另外一个目录 

示例：

```java
Path sourcePath = Paths.get("d:\\att\\1.txt");
Path destinationPath = Paths.get("d:\\att\\att\\002.txt");
try {
    Files.copy(sourcePath, destinationPath);
} catch (FileAlreadyExistsException e) {
    // 文件已经存在
} catch (IOException e) {
    // 其他异常
    e.printStackTrace();
}
```

首先，该示例创建两个 Path 实例。然后，这个例子调用 Files.copy()，将两个 Path 实例作为参数传递。这可以让源路径引用的文件被复制到目标路径引用的文件中。

如果目标文件已经存在，则抛出一个 java.nio.file.FileAlreadyExistsException 异常。 如果有其他错误，则会抛出一个 IOException。例如，如果将该文件复制到不存在的 目录，则会抛出 IOException。

##### 2）覆盖已存在的文件

Files.copy()方法的第三个参数。如果目标文件已经存在，这个参数指示 copy()方法覆 盖现有的文件。

```java
Files.copy(sourcePath, destinationPath, StandardCopyOption.REPLACE_EXISTING);
```

#### 3、Files.move()

Files.move()用于将文件从一个路径移动到另一个路径。移动文件与重命名相同，但是 移动文件既可以移动到不同的目录，也可以在相同的操作中更改它的名称。 

示例：

```java
Path sourcePath = Paths.get("d:\\att\\1.txt");
Path destinationPath = Paths.get("d:\\att\\001.txt");
try {
    Files.move(sourcePath, destinationPath, StandardCopyOption.REPLACE_EXISTING);
} catch (IOException e) {
    // 移动文件失败
    e.printStackTrace();
}
```

Files.move()的第三个参数。这个参数告诉 Files.move()方法来覆盖目标路径上的任何 现有文件。

#### 4、Files.delete()

Files.delete()方法可以删除一个文件或者目录。 

示例：

```java
Path path = Paths.get("d:\\att\\att\\002.txt");
try {
    Files.delete(path);
} catch (IOException e) {
    // 删除文件失败
    e.printStackTrace();
}
```

创建指向要删除的文件的 Path。然后调用 Files.delete()方法。如果 Files.delete()不 能删除文件(例如，文件或目录不存在)，会抛出一个 IOException。

#### 5、Files.walkFileTree()

（1）Files.walkFileTree()方法包含递归遍历目录树功能，将 Path 实例和 FileVisitor 作为参数。Path 实例指向要遍历的目录，FileVisitor 在遍历期间被调用。

（2）FileVisitor 是一个接口，必须自己实现 FileVisitor 接口，并将实现的实例传递给 walkFileTree()方法。在目录遍历过程中，您的 FileVisitor 实现的每个方法都将被调 用。如果不需要实现所有这些方法，那么可以扩展 SimpleFileVisitor 类，它包含 FileVisitor 接口中所有方法的默认实现。

（3）FileVisitor 接口的方法中，每个都返回一个 FileVisitResult 枚举实例。 FileVisitResult 枚举包含以下四个选项:

- CONTINUE 继续 
- TERMINATE 终止 
- SKIP_SIBLING 跳过同级 
- SKIP_SUBTREE 跳过子级

**（4）查找一个名为001.txt 的文件示例：**

```java
    public static void main(String[] args) {
        Path rootPath = Paths.get("d:\\att");
        String targetFile = File.separator + "1.txt";

        try {
            Files.walkFileTree(rootPath, new SimpleFileVisitor<Path>() {
                @Override
                public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) throws IOException {
                    String fileString = file.toAbsolutePath().toString();
//                    System.out.println("pathString= " + fileString);
                    if (fileString.endsWith(targetFile)) {
                        System.out.println("file found at path: " + file.toAbsolutePath());
                        return FileVisitResult.TERMINATE;
                    }
                    return FileVisitResult.CONTINUE;
                }
            });
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

（5）java.nio.file.Files 类包含许多其他的函数，有关这些方法的更多信息，请查看 java.nio.file.Files 类的 JavaDoc。

### 6.3 AsynchronousFileChannel

在 Java 7 中，Java NIO中添加了 AsynchronousFileChannel，也就是是异步地将数据写入文件。

#### 1、创建 AsynchronousFileChannel

通过静态方法open()创建 

示例：

```java
Path path = Paths.get("d:\\att\\01.txt");

try {
    AsynchronousFileChannel asynchronousFileChannel = AsynchronousFileChannel.open(path, StandardOpenOption.READ);
} catch (IOException e) {
    e.printStackTrace();
}
```

open()方法的第一个参数指向与 AsynchronousFileChannel 相关联文件的Path 实 例。

第二个参数是一个或多个打开选项，它告诉AsynchronousFileChannel 在文件上执 行什么操作。在本例中，我们使用了 StandardOpenOption.READ选项，表示该文件 将被打开阅读。

#### 2、通过 Future 读取数据

可以通过两种方式从 AsynchronousFileChannel 读取数据。第一种方式是调用返回 Future 的 read()方法

示例：

```java
    @Test
    public void asyncFileChannelFuture() throws Exception {
        // 1.创建asyncFileChannel,异步文件通道对象
        Path path = Paths.get("d:\\att\\01.txt");
        AsynchronousFileChannel asynchronousFileChannel = AsynchronousFileChannel.open(path, StandardOpenOption.READ);
        // 2.创建buffer
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        // 3. 调用channel的read方法获得future
        long positon = 0;
        Future<Integer> future = asynchronousFileChannel.read(buffer, positon);
        // 4. 判断是否完成 isDone
        while (!future.isDone()) ;
        // 5. 读取数据到buffer
        buffer.flip();
//            while (buffer.hasRemaining()) {
//                System.out.print((char) buffer.get());
//            }
        byte[] data = new byte[buffer.limit()];
        buffer.get(data);
        System.out.println(new String(data));
        buffer.clear();
        asynchronousFileChannel.close();
    }
```

上述代码： 

（1）创建了一个AsynchronousFileChannel， 

（2）创建一个ByteBuffer，它被传递给 read()方法作为参数，以及一个 0 的位置。 

（3）**在调用 read()之后，循环，直到返回的 isDone()方法返回 true。** 

（4）读取操作完成后，数据读取到 ByteBuffer 中，然后打印到 System.out 中

#### 3、通过CompletionHandler 读取数据

第二种方法是调用 read()方法，该方法将一个CompletionHandler 作为参数

示例：

```java
@Test
public void asyncFileChannelHandler() throws Exception {
    // 1.创建asyncFileChannel,异步文件通道对象
    Path path = Paths.get("d:\\att\\01.txt");
    AsynchronousFileChannel asynchronousFileChannel = AsynchronousFileChannel.open(path, StandardOpenOption.READ);
    // 2.创建buffer
    ByteBuffer buffer = ByteBuffer.allocate(1024);
    long positon = 0;
    asynchronousFileChannel.read(buffer, positon, buffer, new CompletionHandler<Integer, ByteBuffer>() {
        @Override
        public void completed(Integer result, ByteBuffer attachment) {
            try {
                System.out.println(Thread.currentThread().getName() + " 我睡一会");
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("result= " + result);
            attachment.flip();
            byte[] data = new byte[attachment.limit()];
            attachment.get(data);
            System.out.println(new String(data));
            attachment.clear();
        }

        @Override
        public void failed(Throwable exc, ByteBuffer attachment) {

        }
    });
    for (int i = 0; i < 10; i++) {
        System.out.println(Thread.currentThread().getName() + " 听说你是异步？" + i * 10);
        TimeUnit.MILLISECONDS.sleep(300);
    }
}
```

（1）读取操作完成，将调用 CompletionHandler 的 completed()方法。
（2）对于 completed()方法的参数传递一个整数，它告诉我们读取了多少字节，以及 传递给 read()方法的“附件”。“附件”是 read()方法的第三个参数。在本代码中， 它是 ByteBuffer，数据也被读取。
（3）如果读取操作失败，则将调用 CompletionHandler 的 failed()方法。



**JDK1.8文档**

```java
public abstract <A> void read(ByteBuffer dst,
                              long position,
                              A attachment,
                              CompletionHandler<Integer,? super A> handler)
```

第一个参数是数据缓冲区buffer.
第二个参数是FileChannel特有的，标识着从文件的哪个位置开始读取
第三个参数是附件：**通过这个参数可以传递对象到回调函数中。**
第四个参数是回调函数的回调逻辑，AIO的回调机制通过实现CompletionHandler接口来实现，CompletionHandler接口分别定义了对应着成功回调函数的completed方法与对应着异常回调函数的failed方法。

#### 4、通过 Future 写数据

和读取一样，可以通过两种方式将数据写入一个AsynchronousFileChannel 

示例：

```java
@Test
public void writeAsyncFileChannelFuture() throws Exception {
    Path path = Paths.get("d:\\att\\01.txt");
    // 创建 AsynchronousFileChannel
    AsynchronousFileChannel fileChannel = AsynchronousFileChannel.open(path, StandardOpenOption.WRITE);
    // 创建buffer
    ByteBuffer buffer = ByteBuffer.allocate(1024);

    buffer.put("你好 async".getBytes());
    buffer.flip();

    long position = 0;
    Future<Integer> future = fileChannel.write(buffer, position);

    while (!future.isDone()) ;
    System.out.println("write done!");
    buffer.clear();
    fileChannel.close();
}
```

首先，AsynchronousFileChannel 以写模式打开。然后创建一个ByteBuffer，并将 一些数据写入其中。然后，ByteBuffer 中的数据被写入到文件中。最后，示例检查返 回的 Future，以查看写操作完成时的情况。

注意，文件必须已经存在。如果该文件不存在，那么write()方法将抛出一个 java.nio.file.NoSuchFileException。

#### 5、通过CompletionHandler 写数据

示例：

```java
@Test
public void writeAsyncFileChannelHandler() throws Exception {
    Path path = Paths.get("d:\\att\\01.txt");
    // 创建 AsynchronousFileChannel
    AsynchronousFileChannel fileChannel = AsynchronousFileChannel.open(path, StandardOpenOption.WRITE);
    // 创建buffer
    ByteBuffer buffer = ByteBuffer.allocate(1024);
    buffer.put("回调式写".getBytes());
    buffer.flip();
    long positon = 0;
    fileChannel.write(buffer, positon, null, new CompletionHandler<Integer, Object>() {
        @Override
        public void completed(Integer result, Object attachment) {
            System.out.println(Thread.currentThread().getName() + " 我睡睡你就知道了");
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("write done!" + "bytes written: " + result);
        }

        @Override
        public void failed(Throwable exc, Object attachment) {
            System.out.println("Write failed");
        }
    });
    for (int i = 0; i < 10; i++) {
        System.out.println(Thread.currentThread().getName() + " 我倒要看看你异步不异步 " + i * 10);
        TimeUnit.MILLISECONDS.sleep(500);
    }
    buffer.clear();
    fileChannel.close();
}
```

当写操作完成时，将会调用 CompletionHandler 的 completed()方法。如果写失败，则会调用 failed()方法。

### 6.4 字符集（Charset）

java 中使用Charset 来表示字符集编码对象

#### Charset 常用静态方法

```java
public static Charset forName(String charsetName)//通过编码类型获得 Charset 对 象
public static SortedMap<String,Charset> availableCharsets()//获得系统支持的所有 编码方式
public static Charset defaultCharset()//获得虚拟机默认的编码方式
public static boolean isSupported(String charsetName)//判断是否支持该编码类型
```

#### Charset 常用普通方法

```java
public final String name()//获得 Charset 对象的编码类型(String) 
public abstract CharsetEncoder newEncoder()//获得编码器对象 
public abstract CharsetDecoder newDecoder()//获得解码器对象
```

#### 代码示例：

```java
    public static void main(String[] args) throws Exception {
        // 1 获取charset对象
        Charset charset = Charset.forName("UTF-8");
        // 2 获取编码器对象
        CharsetEncoder charsetEncoder = charset.newEncoder();
        // 3 创建buffer
        CharBuffer buffer = CharBuffer.allocate(100);
        buffer.put("你好，charset");
        buffer.flip();
        // 4 编码
        ByteBuffer encodeBuffer = charsetEncoder.encode(buffer);
        // 输出编码后的内容
        for (int i = 0; i < encodeBuffer.limit(); i++) {
            System.out.println("编码后结果：" + encodeBuffer.get());
        }
        encodeBuffer.flip();
        // 5 获取解码器对象
        CharsetDecoder charsetDecoder = charset.newDecoder();
        // 6 解码
        CharBuffer decodeBuffer = charsetDecoder.decode(encodeBuffer);
        // 输出解码后的内容
        System.out.println("解码后结果：" + decodeBuffer.toString());

        System.out.println("尝试使用其他格式解码：");
        encodeBuffer.flip();
        Charset gbk = Charset.forName("GBK");
        CharBuffer decode2 = gbk.decode(encodeBuffer);
//        CharsetDecoder charsetDecoder2 = gbk.newDecoder();
//        CharBuffer decode2 = charsetDecoder2.decode(encodeBuffer);
        System.out.println(decode2.toString());
        // 查看所有Charset支持的字符编码
        SortedMap<String, Charset> charsetSortedMap = Charset.availableCharsets();
        Set<Map.Entry<String, Charset>> entrySet = charsetSortedMap.entrySet();
        for (Map.Entry<String, Charset> entry : entrySet) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }
```

## 第 7 章 Java NIO 综合案例 

**使用 Java NIO 完成一个多人聊天室功能**

### 7.1 服务端代码

### 7.2 客户端代码

# AIO

## AIO概述

在2011年7月28日，jdk1.7被正式发布。他的一个最大的亮点就是将原来的NIO类库生成到了NIO2.0，也被叫做AIO。

Java正式提供了异步文件I/O操作，同时提供了与UNIX网络编程事件驱动I/O对应的AIO。

**AIO**提供了从建立连接到读、写的全异步操作。**AIO**可用于异步的**文件读写**和**网络通信**。

AIO的原理是在之前的基础上进行的改进，意思是异步非阻塞式IO，也就是说你的客户端在进行读写操作的时候，只需要给服务器发送一个请求，不用一直等待回答就可以去做其他的事了。

AIO最大的一个特性就是异步能力，这种能力对socket与文件I/O都起作用。AIO其实是一种在读写操作结束之前允许进行其他操作的I/O处理。AIO是对JDK1.4中提出的同步非阻塞I/O(NIO)的进一步增强。



## AIO相关的类

增加的新的类如下:

- AsynchronousChannel：支持异步通道，包括服务端AsynchronousServerSocketChannel和普通AsynchronousSocketChannel等实现。
- CompletionHandler：用户处理器。定义了一个用户处理就绪事件的接口，由用户自己实现，异步io的数据就绪后回调该处理器消费或处理数据。
- AsynchronousChannelGroup：一个用于资源共享的异步通道集合。处理IO事件和分配给CompletionHandler

另外，主要在java.nio.channels包下增加了下面四个异步通道：

- AsynchronousSocketChannel
- AsynchronousServerSocketChannel
- AsynchronousFileChannel
- AsynchronousDatagramChannel



AIO的实施需充分调用OS参与，IO需要操作系统支持、并发也同样需要操作系统的支持，所**以性能方面不同操作系统差异会比较明显**。因此在实际中AIO使用不是很广泛。

## AIO的异步方式

AIO的异步方式分为两种：一种是使用Future类达到异步的未来式，一种是类似于AJax采用回调函数实现异步的回调式。

### 未来式

未来式通过concurrent包下的Future类达到异步的目的。提起Future类，自然就会联想到线程池，Future类用来获取Submit给线程池的Callable任务的返回结果。

### 回调式

回调式类似于Ajax进行异步请求的机制，在进行IO操作之前分别定义成功的回调函数和失败的回调函数，AIO会根据IO操作的结果在结束后执行相应的回调函数来实现异步。

## 异步原理

JDK底层通过线程池`ThreadPoolExecutor`来执行回调通知，异步回调通知类由`sun.nio.ch.AsynchronousChannelGroupImpl`实现，它经过层层调用，最终回调`com.phei.netty.aio.AsyncTimeClientHandler$1.completed`方法，完成回调通知。

异步Socket Channel是被动执行对象，我们不需要像NIO编程那样创建一个独立的I/O线程来处理读写操作。对于`AsynchronousServerSocketChannel`和`AsynchronousSocketChannel`它们都由JDK底层的线程池负责回调并驱动读写操作。正因为如此，基于NIO 2.0新的异步非阻塞Channel进行编程比NIO编程更为简单。

## Reactor与Proactor

- 两种IO多路复用方案:Reactor and Proactor。
- Reactor模式是基于同步I/O的，而Proactor模式是和异步I/O相关的。
- reactor：能收了你跟俺说一声。proactor: 你给我收十个字节，收好了跟俺说一声。

## AIO的缺点

1、实现复杂

上面的代码量你已经看到了，恶心到不能恶心。实现这么一个简单的功能就要写这么多。

2、需要额外的技能

也就是说你想要学号AIO，还需要java多线程的技术做铺垫才可以。否则我们很难写出质量高的代码。

3、一个著名的Selector空轮询bug

它会导致CPU100%，之前在我的群里面，有人曾经遇到过这个问题，而且官方说在1.6的版本中解决，但是现在还有。遇到的时候我们虽然可以解决但是不知道的人会很痛苦。

4、可靠性差

也就是说我们的网络状态是复杂多样的，会遇到各种各样的问题，比如说网断重连、缓存失效、半包读写等等。可靠性比较差。稍微出现一个问题，还需要大量的代码去完善。

当然还有很多其他的缺点，不过就单单第一条估计就很难发展。后来出现了更加牛的网络通信框架netty。

# 问题

## DatagramSocket类的bind（）和connect（）的区别？

**源码注释：**

connect（）：连接此通道的套接字

bind（）：将此 DatagramSocket 绑定到特定地址和端口



**网友解释：**

socket() 函数用来创建套接字，确定套接字的各种属性，然后服务器端要用 bind() 函数将套接字与特定的IP地址和端口绑定起来，只有这样，流经该IP地址和端口的数据才能交给套接字处理；而客户端要用 connect() 函数建立连接。



## UDP中使用bind和connect的做用？

1. UDP中可使用connect系统调用

2. UDP中connect操做与TCP中connect操做有着本质区别。
   - TCP中调用connect会引发三次握手,client与server创建连结.UDP中调用connect内核仅仅把对端ip&port记录下来.

3. UDP中能够屡次调用connect,TCP只能调用一次connect.  

   - UDP屡次调用connect有两种用途:1,指定一个新的ip&port连结. 2,断开和以前的ip&port的连结.

   - 指定新连结,直接设置connect第二个参数便可.

   - 断开连结,须要将connect第二个参数中的sin_family设置成 AF_UNSPEC便可. 

4. UDP中使用connect能够提升效率.缘由以下:
   - 普通的UDP发送两个报文内核作了以下:#1:创建连结#2:发送报文#3:断开连结#4:创建连结#5:发送报文#6:断开连结
   - 采用connect方式的UDP发送两个报文内核以下处理:#1:创建连结#2:发送报文#3:发送报文另一点,  每次发送报文内核都由可能要作路由查询.
5. 采用connect的UDP发送接受报文能够调用send,write和recv,read操做.固然也能够调用sendto,recvfrom.
   - 调用sendto的时候第五个参数必须是NULL,第六个参数是0.
   - 调用recvfrom,recv,read系统调用只能获取到先前connect的ip&port发送的报文. 

## UDP中使用connect的好处?

1:会提高效率.前面已经描述了.

2:高并发服务中会增长系统稳定性.缘由:假设client A 经过非connect的UDP与server B,C通讯.B,C提供相同服务.为了负载均衡,咱们让A与B,C交替通讯.A 与 B通讯IPa:PORTa <----> IPb:PORTb；

## 对一个UDP的套接口屡次调用connect的状况如何

- 链接新的IP和端口
- 断开前面的链接

第一个目的不一样于TCP链接connect的使用：对于TCP链接，connect只能调用一次；针对UDP则能够connect到不一样的server，eg：client须要和多个服务器同时通讯。

第二个目的为了断开一个已链接的UDP链接，再次调用connect时，把套接字地址结构的地址簇成员（IPv4为sin_family，IPv6为sin6_family），设置为AF_UNSPEC便可。

 

性能：当进程知道本身要给同一个目的地址发送多个数据报时，显式链接套接字效率更高。而临时链接未链接的UDP套接字大约会消耗每一个UDP传输三分之一的开销。



## NIO中 channel与pipe？

文章：https://blog.csdn.net/baiye_xing/article/details/73135566

Channel 表示 IO 源与目标打开的连接。Channel 类似于传统的“流”。只不过Stream是单向的，如：InputStream, OutputStream.而**Channel是双向的**，既可以用来进行读操作，又可以用来进行写操作。但Channel 本身不能直接访问数据，Channel 只能与Buffer 进行交互。


Java NIO **pipe 管道是2个线程之间的单向数据连接**。Pipe有一个source通道和一个sink通道。数据会被写到sink通道，从source通道读取



channel -> io通道（远程或本地）

pipe -> jvm进程内的线程间管道







在bio中没有数据read会等待，nio选择器轮训管道的状态，没有数据就会继续轮询，线程不会等待，直到有读或写的事件在处理

selector是观察者模式

**记住 阻塞和非阻塞底层都是操作系统的内核接口提供的**

这个不是阻塞 他相当于休眠 释放CPU资源 当有事件发生会唤醒

select等待客户端socket事件的时候是阻塞的，只不过可以设置阻塞等待的超时时间

多路复用技术，实际就是多了个Selector 选择器，由这个选择器轮询处理多个IO，实际还是阻塞嘛，只不过对于选择器之上的调用来说是非阻塞

select poll epoll 操作系统内核实现，多路复用器





监听器模式