## 前言

在一些数据量比较大的场景中，做一些查重、排序，一般的方法难以实现。数据量过大，会占用较大的内存，常用的处理方式有两种：BitMap（位图法）和布隆过滤。

## 概述

所谓bitmap就是用一个bit位来标记某个元素对应的value，而key即是这个元素。由于采用bit为单位来存储数据，因此在可以大大的节省存储空间。

用bit来表示一个int数，节约了31倍的内存空间，即int（4*8），bit（8/1)，所以数据量越大使用这种方式的优势越明显，前提是场景适用这种方式。

## 原理

在Java中，一个int类型占32个字节，我们用一个int数组来表示时:new int[32],总计占用内存32*32bit,现假如我们用int字节码的每一位表示一个数字的话，那么32个数字只需要一个int类型所占内存空间大小就够了，这样在数据量大的情况下会节省很多内存

#### 具体思路

1个int占4字节即4*8=32位，那么我们只需要申请一个int数组长度为 n/32+1 (`int[] array = new int[n/32+1];`)即可存储完这些数据，其中n代表要进行查找的总数，array中的每个元素在内存占32位,可以对应表示十进制数0~31,所以可得到BitMap表:

```java
array[0]:可表示0~31

array[1]:可表示32~63

array[2]可表示64~95

.......

```

## 实例解析

### 题目

10亿个正整数，给定一个数值，如何快速排定该数值是否在10亿个正整数当中？

位图法的思想类似于hash寻址，首先初始化一个int数组，每个元素对应32位比特，将10亿个元素分别读入内存，对int数组中的元素比特位进行标记，如果存在，标记为1即可。标记完之后，即可快速判定某个元素是否在10亿个正整数当中，时间复杂度为O（1）

### 关键问题

1. 需要多大的内存？

   ```
   10亿个元素需要10亿个比特位，：10亿/8=1亿2500万字节，1亿2500万/1024=122,070.3125KB=119.2092895507813MB，约为120MB
   ```

2. 需要申请多大的数组？

   ```
   array[0]:可表示0~31
   array[1]:可表示32~63
   array[2]可表示64~95
   ···
   数组长度为：10亿/32+1=31,250,001
   ```

   每个int类型可标识32个整数，如下图：

   ![img](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/11/202111021923974.png)

### 如何寻址

以 119 为例，如何确定其数组位置和所在的比特位？

- 数组位置：

  ```java
  index = 119 >> 5 = 3
   // 119/32
  // 也就是第四个数组元素，array[3]
  ```

- 比特位：

  ```java
  bitIndex = (32-1)&119 = 23 // 第23个比特位（从0开始）
  // 该算法的结果和模运算的结果是相同的。但是，对于现代的处理器来说，除法和求余数（模运算）是最慢的动作。
  // a % b == (b-1) & a 当b是2的指数时等式成立
  ```

### 设置比特位

#### 将比特位设置为 1

将第23个比特位设置为 1

只需将 1 左移 23位，然后和原来的数进行或运算

```java
public static void main(String[] args) {
    int[] array = new int[10];
    array[3] = 104;
    System.out.println("array[3] =" + Integer.toBinaryString(array[3]));
    // 添加 119
    int num = 119;
    int index = num >> 5; // 3
    int bitIndex = num & 31;  // (32 - 1)    23
    array[3] |= 1 << bitIndex;// 1 左移 23位，然后与原来的值进行与运算
    System.out.println("1 << bitIndex: " + Integer.toBinaryString(1 << bitIndex));// 1000 0000 0000 0000 0000 0000
    System.out.println("array[3] = " + Integer.toBinaryString(array[3]));            // 1000 0000 0000 0000 0110 1000
}
```

输出结果：

```java
array[3] =1101000
1 << bitIndex: 100000000000000000000000
array[3] = 100000000000000001101000
```

#### 将比特位设置为 0

将第23个比特位设置为 0

只需1 左移 23位，进行非运算，然后与原来的值进行与运算

```java
public static void main(String[] args) {
    int[] array = new int[10];
    array[3] = 8388608;// 1000 0000 0000 0000 0000 0000
    System.out.println("array[3] =" + Integer.toBinaryString(array[3]));
    // 删除 119
    int num = 119;
    int index = num >> 5; // 3
    int bitIndex = num & 31;  // (32 - 1)    23
    array[3] &= ~(1 << bitIndex);// 1 左移 23位，进行非运算，然后与原来的值进行与运算
    System.out.println("~(1 << bitIndex)= " + Integer.toBinaryString(~(1 << bitIndex)));// 11111111011111111111111111111111
    System.out.println("array[3] = " + Integer.toBinaryString(array[3]));            // 0000 0000
}
```

输出结果：

```java
array[3] =100000000000000000000000
~(1 << bitIndex)= 11111111011111111111111111111111
array[3] = 0
```

#### 判断某一元素是否存在

判断 23 位比特位是否有元素存在：
只需将 1 左移 23 位数，然后与原来的值进行与运算。只要与运算结果中有1，即表示元素存在。所以可以用运行结果是否为0作为元素是否存在的依据

```java
public static void main(String[] args) {
    int[] array = new int[10];
    array[3] = 8388608;// 1000 0000 0000 0000 0000 0000
    System.out.println("array[3] =" + Integer.toBinaryString(array[3]));
    // 判断119是否存在
    int num = 119;
    int index = num >> 5; // 3
    int bitIndex = num & 31;  // (32 - 1)    23
    array[3] &= 1 << bitIndex;// 1 左移 23位，然后与原来的值进行与运算
    System.out.println("1 << bitIndex = " + Integer.toBinaryString(1 << bitIndex));// 100000000000000000000000
    System.out.println("array[3] = " + Integer.toBinaryString(array[3]));            // 100000000000000000000000
    System.out.println("array[3] != 0 ?");
    System.out.println(array[3] != 0);
}
```

输出结果：

```java
array[3] =100000000000000000000000
1 << bitIndex = 100000000000000000000000
array[3] = 100000000000000000000000
array[3] != 0 ?
true
```

### BitMap代码实现

```java
/**
 * @author miemiehoho
 * @date 2021/11/2 20:21
 */
public class BitMap {
    private int[] bitMap;

    public BitMap(long size) {
        int len = (int) (size >> 5) + 1;
        bitMap = new int[len];
    }

    // 添加元素，将比特位设置为 1
    public void add(int val) {
        int idx = val >> 5;// 数组位置
        int bitIdx = val & 31;// 比特位置
        bitMap[idx] |= 1 << bitIdx;// 1 左移 bitIdx 位，然后与原来的值进行与运算
    }

    // 删除元素，将比特位设置为 0
    public void remove(int val) {
        int idx = val >> 5;// 数组位置
        int bitIdx = val & 31;// 比特位置
        bitMap[idx] &= ~(1 << bitIdx);// 1 左移 bitIdx 位，进行非运算，然后与原来的值进行与运算
    }

    // 判断是否存在元素 val
    public boolean contains(int val) {
        int idx = val >> 5;// 数组位置
        int bitIdx = val & 31;// 比特位置
        return (bitMap[idx] & (1 << bitIdx)) != 0 ? true : false;// 1 左移 bitIdx 位，然后与原来的值进行与运算
    }

    public static void main(String[] args) {
        int[] arrays = new int[]{1, 2, 35, 22, 56, 334, 245, 2234, 54};
        BitMap bitMap = new BitMap(2234);
        for (int i:arrays){
            bitMap.add(i);
        }
        System.out.println(bitMap.contains(35));
    }
}
```

## BitMap 优缺点

**BitMap的优点**

1. 效率高
2. 占用内存少，如N=10000000;只需占用内存为N/8 = 1250000Bytes = 1.2M，如果采用int数组存储，则需要1.2M * 32即38.4M
3. 实现简单，适合数据量比较大的场景

**BitMap的缺点**

1. 无法对存在重复的数据进行排序和查找
2. （占用内存）申请的数组长度不好控制，和最大的数值有关：当某个值特别大的情况下，映射的数组超过申请的数组容量，会出现下标越界。这也是上面提到的10亿个元素占用120M是最小内存的原因，实际可能会大于这个内存。
   - 在实际中一般会用谷歌的优化版EWAHCompressedBitmap是跳跃式申请的所以一般不会出现越界的情况

