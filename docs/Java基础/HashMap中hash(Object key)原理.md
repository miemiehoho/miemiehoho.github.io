## 什么是 hash函数

hash函数的实现是一种算法，就是通过一系列的算法来得到一个hash值，通过hash函数得到的hash值就存在hash表中，也就是说hash表是由hash值组成的。

### 什么是 hashcode

hashcode是通过hash函数得到的，它在hash表中有对应的位置

每个对象都有hashcode：

- 首先一个对象肯定有物理地址，网上有人把对象的hashcode说成是对象的地址，事实上这种看法是不全面的，确实有些JVM在实现时是直接返回对象的存储地址，但是大多时候并不是这样，只能说可能存储地址有一定关联
- 那么对象如何得到hashcode呢？通过对象的内部地址(也就是物理地址)转换成一个整数，然后该整数通过hash函数的算法就得到了hashcode(不同jvm的实现不同, hotspot的实现贴在了最后)
- 所以，hashcode是什么呢？就是在hash表中对应的位置
- 举个例子，hash表中有 hashcode为1、hashcode为2、(...)3、4、5、6、7、8这样八个位置，有一个对象A，A的物理地址转换为一个整数17(这是假如)，就通过直接取余算法，17%8=1，那么A的hashcode就为1，且A就在hash表中1的位置

## 为什么使用 hashcode

- HashCode的存在主要是为了查找的快捷性, HashCode是用来在散列存储结构中确定对象的存储地址的 ( 用hashcode来代表对象在hash表中的位置 ) 

- hashCode 存在的重要的原因之一就是在 HashMap(HashSet 其实就是HashMap) 中使用（其实Object 类的 hashCode 方法注释已经说明了 ），HashMap 之所以速度快，因为他使用的是散列表，根据 key 的 hashcode 值生成数组下标（通过内存地址直接查找，不需要判断, 但是需要多出很多内存，相当于以空间换时间）

  ```
   比如：我们有一个能存放1000个数这样大的内存中，在其中要存放1000个不一样的数字，用最笨的方法，就是存一个数字，就遍历一遍，看有没有相同得数，当存了900个数字，开始存901个数字的时候，就需要跟900个数字进行对比，这样就很麻烦，很是消耗时间，用hashcode来记录对象的位置，来看一下。hash表中有1、2、3、4、5、6、7、8个位置，存第一个数，hashcode为1，该数就放在hash表中1的位置，存到100个数字，hash表中8个位置会有很多数字了，1中可能有20个数字，存101个数字时，他先查hashcode值对应的位置，假设为1，那么就有20个数字和他的hashcode相同，他只需要跟这20个数字相比较(equals)，如果没一个相同，那么就放在1这个位置，这样比较的次数就少了很多，实际上hash表中有很多位置，这里只是举例只有8个，所以比较的次数会让你觉得也挺多的，实际上，如果hash表很大，那么比较的次数就很少很少了。  通过对原始方法和使用hashcode方法进行对比，我们就知道了hashcode的作用，并且为什么要使用hashcode了
  ```

  ### equals方法和hashcode的关系
  
 - [hashCode与equals区别](./hashCode与equals区别.md)

归纳总结：

1. 若重写了equals(Object obj)方法，则有必要重写hashCode()方法。

2. 若两个对象equals(Object obj)返回true，则hashCode（）有必要也返回相同的int数。

3. 若两个对象equals(Object obj)返回false，则hashCode（）不一定返回不同的int数。

4. 若两个对象hashCode（）返回相同int数，则equals（Object obj）不一定返回true。

5. 若两个对象hashCode（）返回不同int数，则equals（Object obj）一定返回false。

6. 同一对象在执行期间若已经存储在集合中，则不能修改影响hashCode值的相关信息，否则会导致内存泄露问题。



## String 类型的hashcode

### 写一个测试

测试用例：

```java
public class StringTest {
    String name;

    public StringTest(String name) {
        this.name = name;
    }

    public static void main(String[] args) {
        Map<StringTest,String> map =new HashMap<>();
        map.put(new StringTest("Hi"),"Hi");
        String Hi = map.get(new StringTest("Hi"));
        System.out.println(Hi);
    }
}
```

输出结果：

```java
null
```

由于没有重写 hashcode 方法，JDK 默认使用 Object 类的 hashcode 方法，返回的是一个虚拟内存地址，而每个对象的虚拟地址都是不同的，所以，这个肯定不会返回 Hi

重写hashcode和equals方法：

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    StringTest that = (StringTest) o;
    return Objects.equals(name, that.name);
}

@Override
public int hashCode() {
    return Objects.hash(name);
}
```

再次运行结果：

```java
Hi
```

### String 类型的hashcode

String类型 hashCode() 方法源码：

```java
/*返回哈希码，String的哈希码计算方式为s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]*/
public int hashCode() {
    int h = hash;
    if (h == 0 && value.length > 0) {
        char val[] = value;

        for (int i = 0; i < value.length; i++) {
            h = 31 * h + val[i];
        }
        hash = h;
    }
    return h;
}
```

代码非常简单，就是以31为权，每一位为字符的ASCII值进行运算，用自然溢出来等效取模，因此，每个不同的字符串，返回的 hashCode 大概率不一样。那么为什么使用 31 呢？



### 为什么大部分 hashcode 方法使用 31

在名著 《Effective Java》第 42 页就有对 hashCode 为什么采用 31 做了说明：

> 之所以使用 31， 是因为他是一个奇素数。如果乘数是偶数，并且乘法溢出的话，信息就会丢失，因为与2相乘等价于移位运算（低位补0）。使用素数的好处并不很明显，但是习惯上使用素数来计算散列结果。 31 有个很好的性能，即用移位和减法来代替乘法，可以得到更好的性能： 31 * i == (i << 5） - i， 现代的 VM 可以自动完成这种优化。这个公式可以很简单的推导出来。

可以看到，使用 31 最主要的还是为了性能。当然用 63 也可以。但是 63 的溢出风险就更大了。那么15 呢？仔细想想也可以。

> 在《Effective Java》也说道：编写这种散列函数是个研究课题，最好留给数学家和理论方面的计算机科学家来完成。我们此次最重要的是知道了为什么使用31。

### HashMap 的 hash 算法的实现原理（为什么右移 16 位，为什么要使用 ^ 位异或）

知道了 hashCode 的生成原理了，我们看看今天的主角，hash 算法。

其实，这个也是数学的范畴，从我们的角度来讲，只要知道这是为了更好的均匀散列表的下标就好了

HashMap 的 hash 算法源码：

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

就是：异或运算+无符号右移运算



但是为什么要异或呢？为什么要移位呢？而且移位16？

在分析这个问题之前，我们需要先看看另一个事情， HashMap 如何根据 hash 值找到数组中的对象，我们看看 get 方法的源码：

```java
/**
 * Implements Map.get and related methods.
 *
 * @param hash hash for key
 * @param key the key
 * @return the node, or null if none
 */
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
        // 我们需要关注下面这一行
        (first = tab[(n - 1) & hash]) != null) {
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        if ((e = first.next) != null) {
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

我们看看代码中注释下方的一行代码：first = tab[(n - 1) & hash])。

使用数组长度减一 与运算 hash 值。这行代码就是为什么要让前面的 hash 方法移位并异或。

**解释如下：**

hashcode为int类型，4个字节32位，为了确保散列性，肯定是32位都能进行散列算法计算是最好的。 

首先要明白，为什么用亦或计算，二进制位计算，a 只可能为0,1，b只可能为0,1。a中0出现几率为1/2,1也是1/2，b同理。 位运算符有三种，|，&，……，或，与，亦或：

a,b进行位运算，有4种可能 00，01, 10, 11 

```
a或b计算 结果为1的几率为3/4,0的几率为1/4 

a与b计算 结果为0的几率为3/4,1的几率为1/4

a亦或b计算 结果为1的几率为1/2,0的几率为1/2 
```

所以，进行亦或计算，得到的结果肯定更为平均，不会偏向0或者偏向1，更为散列。 

右移16位进行亦或计算，我将其拆分为两部分：前16位的亦或运算，和后16位的亦或运算

后16位的亦或运算，即原hashcode后16位与原hashcode前16位进行亦或计算，得出的结果，前16位和后16位都有参与其中，保证了 32位全部进行计算。 

前16位的亦或运算，即原hasecode前16位与0000 0000 0000 0000进行亦或计算，结果只与前16位hashcode有关，同时亦或计算，保证 结果为0的几率为1/2,1的几率为1/2，也是平均的。 

**所以为什么是右移16位：**

（1）结果的后16位保证了hashcode32位全部参与计算，也保证了0,1平均，散列性 

（2）结果的前16位保证hashcode前16位的0，1平均散列性，附带hashcode前16位参与计算。

  (3) 16与16位数相同，利于计算，不需要补齐，移去位数数据 更多情况，hashmap只会用到前16位（临时数据一般不会这么大）

所以（1）占主因

```
我们分析一下：

首先，假设有一种情况，对象 A 的 hashCode 为 1000010001110001000001111000000，对象 B 的 hashCode 为 0111011100111000101000010100000。

如果数组长度是16，也就是 15 与运算这两个数， 你会发现结果都是0。这样的散列结果太让人失望了。很明显不是一个好的散列算法。

但是如果我们将 hashCode 值右移 16 位，也就是取 int 类型的一半，刚好将该二进制数对半切开。并且使用位异或运算（如果两个数对应的位置相反，则结果为1，反之为0），这样的话，就能避免我们上面的情况的发生。

总的来说，使用位移 16 位和 异或 就是防止这种极端情况。但是，该方法在一些极端情况下还是有问题，比如：10000000000000000000000000 和 10000000001000000000000000 这两个数，如果数组长度是16，那么即使右移16位，在异或，hash 值还是会重复。但是为了性能，对这种极端情况，JDK 的作者选择了性能。毕竟这是少数情况，为了这种情况去增加 hash 时间，性价比不高。
```



### HashMap 为什么使用 & 与运算代替模运算？

我们再看看刚刚说的那个根据hash计算下标的方法：

tab[(n - 1) & hash]；

其中 n 是数组的长度。其实该算法的结果和模运算的结果是相同的。但是，对于现代的处理器来说，除法和求余数（模运算）是最慢的动作。

上面情况下和模运算相同

a % b == (b-1) & a ,**当b是2的指数时，等式成立**。

我们说 & 与运算的定义：与运算 第一个操作数的的第n位于第二个操作数的第n位如果都是1，那么结果的第n为也为1，否则为0；

当 n 为 16 时， 与运算 101010100101001001101 时，也就是
1111 & 101010100101001001000 结果：1000 = 8
1111 & 101000101101001001001 结果：1001 = 9
1111 & 101010101101101001010 结果： 1010 = 10
1111 & 101100100111001101100 结果： 1100 = 12

可以看到，当 n 为 2 的幂次方的时候，减一之后就会得到 1111* 的数字，这个数字正好可以掩码。并且得到的结果取决于 hash 值。因为 hash 值是1，那么最终的结果也是1 ，hash 值是0，最终的结果也是0。

### HashMap 的容量为什么建议是 2的幂次方？

到这里，我们提了一个关键的问题： HashMap 的容量为什么建议是 2的幂次方？正好可以和上面的话题接上。楼主就是这么设计的。

为什么要 2 的幂次方呢？

我们说，hash 算法的目的是为了让hash值均匀的分布在桶中（数组），那么，如何做到呢？试想一下，如果不使用 2 的幂次方作为数组的长度会怎么样？

假设我们的数组长度是10，还是上面的公式：
1010 & 101010100101001001000 结果：1000 = 8
1010 & 101000101101001001001 结果：1000 = 8
1010 & 101010101101101001010 结果： 1010 = 10
1010 & 101100100111001101100 结果： 1000 = 8

看到结果我们惊呆了，这种散列结果，会导致这些不同的key值全部进入到相同的插槽中，形成链表，性能急剧下降。

所以说，我们一定要保证 & 中的二进制位全为 1，才能最大限度的利用 hash 值，并更好的散列，只有全是1 ，才能有更多的散列结果。如果是 1010，有的散列结果是永远都不会出现的，比如 0111，0101，1111，1110…，只要 & 之前的数有 0， 对应的 1 肯定就不会出现（因为只有都是1才会为1）。大大限制了散列的范围。

### 我们自定义 HashMap 容量最好是多少？

那我们如何自定义呢？如果我们预计我们的散列表中有2个数据，那么我就初始化容量为2嘛？

绝对不行，如果大家看过源码就会发现，**如果Map中已有数据的容量达到了初始容量的 75%，那么散列表就会扩容**，而扩容将会重新将所有的数据**重新散列，性能损失严重**，所以，我们可以必须要大于我们预计数据量的 1.34 倍，如果是2个数据的话，就需要初始化 2.68 个容量。当然这是开玩笑的，2.68 不可以，3 可不可以呢？肯定也是不可以的，我前面说了，如果不是2的幂次方，散列结果将会大大下降。导致出现大量链表。那么我可以将初始化容量设置为4。 当然了，如果你预计大概会插入 12 条数据的话，那么初始容量为16简直是完美，一点不浪费，而且也不会扩容。

如果某个map很大，注意，肯定是事先没有定义好初始化长度，假设，某个Map存储了10000个数据，那么他会扩容到 20000，实际上，根本不用 20000，只需要 10000* 1.34= 13400 个，然后向上找到一个2 的幂次方，也就是 16384 初始容量足够。


在日常开发中，可以使用

```java
Map<String, String> map = Maps.newHashMapWithExpectedSize(10);
```

来创建一个HashMap，计算的过程guava会帮我们完成，这是一种用内存换性能的做法，真正使用的时候，要考虑到内存的影响。

**在JDK 8中，putAll方法采用了这种方式，而put、构造函数等并没有默认使用这个公式。**



**Hotpot虚拟机生成hash散列值的实现：**

```c++
static inline intptr_t get_next_hash(Thread * Self, oop obj) {
  intptr_t value = 0 ;
  if (hashCode == 0) {
     // This form uses an unguarded global Park-Miller RNG,
     // so it's possible for two threads to race and generate the same RNG.
     // On MP system we'll have lots of RW access to a global, so the
     // mechanism induces lots of coherency traffic.
     value = os::random() ;
  } else
  if (hashCode == 1) {
     // This variation has the property of being stable (idempotent)
     // between STW operations.  This can be useful in some of the 1-0
     // synchronization schemes.
     intptr_t addrBits = intptr_t(obj) >> 3 ;
     value = addrBits ^ (addrBits >> 5) ^ GVars.stwRandom ;
  } else
  if (hashCode == 2) {
     value = 1 ;            // for sensitivity testing
  } else
  if (hashCode == 3) {
     value = ++GVars.hcSequence ;
  } else
  if (hashCode == 4) {
     value = intptr_t(obj) ;
  } else {
     // Marsaglia's xor-shift scheme with thread-specific state
     // This is probably the best overall implementation -- we'll
     // likely make this the default in future releases.
     unsigned t = Self->_hashStateX ;
     t ^= (t << 11) ;
     Self->_hashStateX = Self->_hashStateY ;
     Self->_hashStateY = Self->_hashStateZ ;
     Self->_hashStateZ = Self->_hashStateW ;
     unsigned v = Self->_hashStateW ;
     v = (v ^ (v >> 19)) ^ (t ^ (t >> 8)) ;
     Self->_hashStateW = v ;
     value = v ;
  }
 
  value &= markOopDesc::hash_mask;
  if (value == 0) value = 0xBAD ;
  assert (value != markOopDesc::no_hash, "invariant") ;
  TEVENT (hashCode: GENERATE) ;
  return value;
}
```

参考文章：

1. https://blog.csdn.net/qq_42034205/article/details/90384772
2. https://blog.csdn.net/q5706503/article/details/85114159

