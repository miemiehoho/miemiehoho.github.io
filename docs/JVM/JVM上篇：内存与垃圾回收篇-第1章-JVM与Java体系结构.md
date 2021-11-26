# JVM的生命周期

##  虚拟机的启动

Java虚拟机的启动是通过引导类加载器（bootstrap class loader）创建一个初始类（initial class）来完成的，这个类是由虚拟机的具体实现指定的。

## 虚拟机的执行

1. 一个运行中的Java虚拟机有着一个清晰的任务：执行Java程序
2. 程序开始执行时他才运行，程序结束时他就停止
3. 执行一个所谓的Java程序的时候，真真正正在执行的是一个叫做Java虚拟机的进程

## 虚拟机的退出

1. 程序正常执行结束

2. 程序在执行过程中遇到了异常或错误而异常终止

3. 由于操作系统用现错误而导致Java虚拟机进程终止

4. 某线程调用Runtime类或System类的exit()方法，或Runtime类的halt()方法，并且Java安全管理器也允许这次exit()或halt()操作。

5. 除此之外，JNI（Java Native Interface）规范描述了用JNI Invocation API来加载或卸载 Java虚拟机时，Java虚拟机的退出情况。

   ```java
   // System类的exit方法
       public static void exit(int status) {
           Runtime.getRuntime().exit(status);
       }
   //Runtime类的exit方法
       public void exit(int status) {
           SecurityManager security = System.getSecurityManager();
           if (security != null) {
               security.checkExit(status);
           }
           Shutdown.exit(status);
       }
   // Runtime类的halt方法
       public void halt(int status) {
           SecurityManager sm = System.getSecurityManager();
           if (sm != null) {
               sm.checkExit(status);
           }
           Shutdown.beforeHalt();
           Shutdown.halt(status);
       }
   // Shutdown类里的exit方法
       static void exit(int status) {
           boolean runMoreFinalizers = false;
           synchronized (lock) {
               if (status != 0) runFinalizersOnExit = false;
               switch (state) {
               case RUNNING:       /* Initiate shutdown */
                   state = HOOKS;
                   break;
               case HOOKS:         /* Stall and halt */
                   break;
               case FINALIZERS:
                   if (status != 0) {
                       /* Halt immediately on nonzero status */
                       halt(status);
                   } else {
                       /* Compatibility with old behavior:
                        * Run more finalizers and then halt
                        */
                       runMoreFinalizers = runFinalizersOnExit;
                   }
                   break;
               }
           }
           if (runMoreFinalizers) {
               runAllFinalizers();
               halt(status);
           }
           synchronized (Shutdown.class) {
               /* Synchronize on the class object, causing any other thread
                * that attempts to initiate shutdown to stall indefinitely
                */
               beforeHalt();
               sequence();
               halt(status);
           }
       }
   // Shutdown类里的halt方法
       /* The halt method is synchronized on the halt lock
        * to avoid corruption of the delete-on-shutdown file list.
        * It invokes the true native halt method.
        */
       static void halt(int status) {
           synchronized (haltLock) {
               halt0(status);
           }
       }
   
       static native void halt0(int status);
   ```

   

# JVM 发展历程

所有虚拟机的原则：一次编译，到处运行

## Sun Classic VM

- 第一款商用虚拟机
- 只提供解释器
- 现在hotspot内置了此虚拟机

## Exact VM

- 具备现在高性能虚拟机的雏形
  - 热点探测
  - 编译器与解释器混合工作模式

## SUN HotSpot VM（商用三大虚拟机之一）

1. HotSpot历史
   - 最初由一家名为“Longview Technologies”的小公司设计
   - 1997年，此公司被Sun收购；2009年，Sun公司被甲骨文收购。
   - JDK1.3时，HotSpot VM成为默认虚拟机
2. 目前**Hotspot占有绝对的市场地位，称霸武林**。
   - 不管是现在仍在广泛使用的JDK6，还是使用比例较多的JDK8中，默认的虚拟机都是HotSpot
   - Sun/oracle JDK和openJDK的默认虚拟机
   - 因此本课程中默认介绍的虚拟机都是HotSpot，相关机制也主要是指HotSpot的GC机制。（比如其他两个商用虚机都没有方法区的概念）
3. 从服务器、桌面到移动端、嵌入式都有应用。
4. 名称中的HotSpot指的就是它的热点代码探测技术。
   - 通过计数器找到**最具编译价值代码**，触发即时编译或栈上替换
   - 通过编译器与解释器协同工作，在最优化的程序响应时间与最佳执行性能中取得平衡

## BEA 的 JRockit（商用三大虚拟机之一）

- 专注于服务器端应用
- JRockit JVM是世界上最快的JVM



## IBM 的 J9（商用三大虚拟机之一）

全称：IBM Technology for Java Virtual Machine，简称IT4J，内部代号：J9



## Azul VM

## Microsoft JVM

## Taobao JVM

## Graal VM

”Run Programs Faster Anywhere"