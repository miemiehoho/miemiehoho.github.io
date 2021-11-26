#### 1. Set<String> setStr=new HashSet<String>()；Set<String> setStr=new HashSet<String>()；SetStr是HashSet（）为什么还要声明为Set？这样做有声明好处？说明了接口的什么特性?





利用接口编程，实现多态，这是java一大优点和优势，在其他方法中如需要用set的参数，就不用写hashset，同时treeset等set的实现都可以传进去的。



这是java多态的一种表现,有时候可能不知道子类需要何种类型而声明一个接口.以后类似Set<String> setStr=new HashSet<String>()；这种声明 其实SetStr是HashSet类型(虽然声明时为Set,但JVM运行时回自动把他转化为HashSet类型的),这种做法就更能体现接口的意义了.