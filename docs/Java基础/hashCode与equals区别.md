## JDK定义和规范

JDK中对对equals(Object obj)和hashCode()这两个方法的定义和规范：在Java中任何一个对象都具备equals(Object obj)和hashCode()这两个方法，因为他们是在Object类中定义的。 equals(Object obj)方法用来判断两个对象是否“相同”，如果“相同”则返回true，否则返回false。 hashCode()方法返回一个int数，在Object类中的默认实现是“将该对象的内部地址转换成一个整数返回”。 

## 归纳总结：

1. 若重写了equals(Object obj)方法，则有必要重写hashCode()方法。

2. 若两个对象equals(Object obj)返回true，则hashCode（）有必要也返回相同的int数。

3. 若两个对象equals(Object obj)返回false，则hashCode（）不一定返回不同的int数。

4. 若两个对象hashCode（）返回相同int数，则equals（Object obj）不一定返回true。

5. 若两个对象hashCode（）返回不同int数，则equals（Object obj）一定返回false。

6. 同一对象在执行期间若已经存储在集合中，则不能修改影响hashCode值的相关信息，否则会导致内存泄露问题。

## 什么情况下需要重写equals和hashCode

一般来说涉及到对象之间的比较大小就需要重写equals方法

但是为什么第一点说重写了equals就需要重写hashCode呢？实际上这只是一条规范，如果不这样做程序也可以执行，只不过会隐藏bug。

一般一个类的对象如果会存储在HashTable，HashSet,HashMap等散列存储结构中，那么重写equals后最好也重写hashCode，否则会导致存储数据的不唯一性（存储了两个equals相等的数据）。而如果确定不会存储在这些散列结构中，则可以不重写hashCode。



![img](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/11/202111021051798.gif)

​																		**对象放入散列集合的流程图**

从上图中可以看到在存储一个对象时，先进行hashCode值的比较，然后进行equals的比较



**测试一：只覆盖equals（Object obj）但不覆盖hashCode（）,导致数据不唯一性**

```java
public class HashCodeTest {
    public static void main(String[] args) {
        Collection set = new HashSet();
        Point p1 = new Point(1, 1);
        Point p2 = new Point(1, 1);

        System.out.println(p1.equals(p2));
        set.add(p1);   //(1)
        set.add(p2);   //(2)
        set.add(p1);   //(3)

        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            Object object = iterator.next();
            System.out.println(object);
        }
    }

}

class Point {
    private int x;
    private int y;

    public Point(int x, int y) {
        super();
        this.x = x;
        this.y = y;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Point other = (Point) obj;
        if (x != other.x)
            return false;
        if (y != other.y)
            return false;
        return true;
    }

    @Override
    public String toString() {
        return "x:" + x + ",y:" + y;
    }

}
```

输出结果：

```java
true
x:1,y:1
x:1,y:1
```

原因分析：
（1）当执行set.add(p1)时（1），集合为空，直接存入集合；

（2）当执行set.add(p2)时（2），首先判断该对象（p2）的hashCode值所在的存储区域是否有相同的hashCode，因为没有覆盖hashCode方法，所以jdk使用默认Object的hashCode方法，返回内存地址转换后的整数，因为不同对象的地址值不同，所以这里不存在与p2相同hashCode值的对象，因此jdk默认不同hashCode值，equals一定返回false，所以直接存入集合。

 （3）当执行set.add(p1)时（3），时，因为p1已经存入集合，同一对象返回的hashCode值是一样的，继续判断equals是否返回true，因为是同一对象所以返回true。此时jdk认为该对象已经存在于集合中，所以舍弃。

**测试二：覆盖hashCode方法，但不覆盖equals方法，仍然会导致数据的不唯一性**

```java
public class HashCodeTest {
    public static void main(String[] args) {
        Collection set = new HashSet();
        Point p1 = new Point(1, 1);
        Point p2 = new Point(1, 1);

        System.out.println(p1.equals(p2));
        set.add(p1);   //(1)
        set.add(p2);   //(2)
        set.add(p1);   //(3)

        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            Object object = iterator.next();
            System.out.println(object);
        }
    }

}

class Point {
    private int x;
    private int y;

    public Point(int x, int y) {
        super();
        this.x = x;
        this.y = y;
    }

    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + x;
        result = prime * result + y;
        return result;
    }

    @Override
    public String toString() {
        return "x:" + x + ",y:" + y;
    }

}
```

输出结果：

```java
false
x:1,y:1
x:1,y:1
```

原因分析：

（1）当执行set.add(p1)时（1），集合为空，直接存入集合；

（2）当执行set.add(p2)时（2），首先判断该对象（p2）的hashCode值所在的存储区域是否有相同的hashCode，这里覆盖了hashCode方法，p1和p2的hashCode相等，所以继续判断equals是否相等，因为这里没有覆盖equals，默认使用'=='来判断，所以这里equals返回false，jdk认为是不同的对象，所以将p2存入集合。

 （3）当执行set.add(p1)时（3），时，因为p1已经存入集合，同一对象返回的hashCode值是一样的，并且equals返回true。此时jdk认为该对象已经存在于集合中，所以舍弃。

**综合上述两个测试，要想保证元素的唯一性，必须同时覆盖hashCode和equals才行**
（注意：在HashSet中插入同一个元素（hashCode和equals均相等）时，会被舍弃，而在HashMap中插入同一个Key（Value 不同）时，原来的元素会被覆盖。）

**测试三：内存泄露问题**

```java
public class HashCodeTest {
    public static void main(String[] args) {
        Collection set = new HashSet();
        Point p1 = new Point(1, 1);
        Point p2 = new Point(1, 2);

        set.add(p1);
        set.add(p2);

        p2.setX(10);
        p2.setY(10);

        set.remove(p2);

        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            Object object = iterator.next();
            System.out.println(object);
        }
    }
}

class Point {
    private int x;
    private int y;

    public Point(int x, int y) {
        super();
        this.x = x;
        this.y = y;
    }


    public int getX() {
        return x;
    }


    public void setX(int x) {
        this.x = x;
    }


    public int getY() {
        return y;
    }


    public void setY(int y) {
        this.y = y;
    }


    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + x;
        result = prime * result + y;
        return result;
    }


    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Point other = (Point) obj;
        if (x != other.x)
            return false;
        if (y != other.y)
            return false;
        return true;
    }


    @Override
    public String toString() {
        return "x:" + x + ",y:" + y;
    }

}
```

输出结果：

```java
x:1,y:1
x:10,y:10
```

原因分析：
       假设p1的hashCode为1，p2的hashCode为2，在存储时p1被分配在1号桶中，p2被分配在2号筒中。这时修改了p2中与计算hashCode有关的信息（x和y）,当调用remove(Object obj)时，首先会查找该hashCode值得对象是否在集合中。假设修改后的hashCode值为10（仍存在2号桶中）,这时查找结果空，jdk认为该对象不在集合中，所以不会进行删除操作。然而用户以为该对象已经被删除，导致该对象长时间不能被释放，造成内存泄露。解决该问题的办法是不要在执行期间修改与hashCode值有关的对象信息，如果非要修改，则必须先从集合中删除，更新信息后再加入集合中

**总结：**

1. hashCode是为了提高在散列结构存储中查找的效率，在线性表中没有作用。
2. equals和hashCode需要同时覆盖。
3. 若两个对象equals返回true，则hashCode有必要也返回相同的int数。
4. 若两个对象equals返回false，则hashCode不一定返回不同的int数,但为不相等的对象生成不同hashCode值可以提高哈希表的性能。
5. 若两个对象hashCode返回相同int数，则equals不一定返回true。
6. 若两个对象hashCode返回不同int数，则equals一定返回false。
7. 同一对象在执行期间若已经存储在集合中，则不能修改影响hashCode值的相关信息，否则会导致内存泄露问题。