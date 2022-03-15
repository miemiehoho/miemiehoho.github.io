# 基本数据结构与算法

## 刷题技巧

1. 能用基本数据类型的就不要用包装类，拆装箱也是很费时间的
2. java优先队列PriorityQueue修改队列内元素排序问题：
   1. PriorityQueue队列是基于堆排序的`不断更新排序`的，没错，它是不断更新排序的。但是前提是要`插入(删除)数据`，如果仅仅是修改已经稳定队列的值或内容，而不进行插入或者删除，那么，这个顺序是`不会变`的
   2. 如果想更新排序，可以这样操作：修改后添加
      q1.remove(team);
      q1.add(team);

## 常用方法

**获取输入数字的高效率方法**

```java
private static StreamTokenizer st = new StreamTokenizer(new BufferedReader(new InputStreamReader(System.in)));
private static int nextInt(){
    try{
        st.nextToken();
        return (int)(st.nval);
    }
    catch(IOException e){
        throw new RuntimeException(e);
    }
}
```

**判断是否数字**

```java
Character.isDigit()
```

## 正则表达式

**匹配多个空格**

```java
String[] strs = s.split("\\s+");
```

## 字符串

**使用join方法拼接字符串**

```java
public String reverseWords(String s) {
    // 去除字符串开头和末尾的空白字符
    s = s.trim();
    // 正则匹配，以空白字符分割
    List<String> stringList = Arrays.asList(s.split("\\s+"));
    Collections.reverse(stringList);
    // 使用join方法拼接字符串
    return String.join(" ",stringList);
    }
```

```java
public String reverseWords(String s) {
    s = s.trim();// 移除首尾空格
    String[] strings = s.split("\\s+");
    Deque<String> stack = new LinkedList<>();
    for (String string : strings) {
        stack.addFirst(string);
    }
    return String.join(" ", stack);
}
```

## 字符

**char 类型 数字转 对应 int 类型值**

```java
int a = Character.getNumericValue(num1.charAt(i));
```

**int类型到char类型强制转换**

```java
int a = '9';
System.out.println((char) a);
// 输出：9
```



## [字符串](/docs/数据结构&算法/字符串/字符串.md)

- KMP
- Manacher

## [单调栈](/docs/数据结构&算法/单调栈/单调栈.md)

## [图论](/docs/数据结构&算法/图论/图论.md)

- 图结构的表达方法
  - 邻接矩阵法
  - 邻接表法
- 并查集
  - 按秩合并
  - 路径压缩
- 图的深度优先搜索算法
- 图的广度优先搜索算法
- 最小生成树相关算法
  - Kruskal 算法
  - Prim 算法
- 单源最短路径相关算法
- 拓扑排序之 Kahn算法

## [有序表](/docs/数据结构&算法/有序表/有序表.md)

## [哈希函数和哈希表](/docs/数据结构&算法/哈希函数和哈希表/README.md)



## [树](/docs/数据结构&算法/树/README.md)

二叉树

二叉搜索树

平衡二叉树

B 树



## [贪心算法](/docs/数据结构&算法/贪心算法/贪心算法.md)

## [排序算法](/docs/数据结构&算法/排序算法/排序算法.md)

## 查找算法

**静态查找算法**

- 顺序查找

- 二分查找（折半查找）

- 分块查找（索引顺序查找）



**动态查找算法**

- 二叉搜索树

- 平衡二叉树
  - AVL树
  - S B 树
  - 红黑树
  - 跳表

- B 树

- 哈希表

## [二分查找](/docs/数据结构&算法/二分查找/二分查找.md)

## 滑动窗口

### [滑动窗口](/docs/数据结构&算法/滑动窗口/滑动窗口.md)

## 暴力递归与动态规划

### [从暴力递归到动态规划](/docs/数据结构&算法/动态规划/从暴力递归到动态规划.md)

## 情景题

## [情景题](/docs/数据结构&算法/情景题/情景题.md)

## [腾讯面试题](/docs/数据结构&算法/腾讯面试题/腾讯面试题.md)

## 经典算法

### [常见算法与经典算法题](/docs/数据结构&算法/常见算法与经典算法题/常见算法与经典算法题.md)



## 时间复杂度

$O(1) < O(log^n) < O(n) < O(n^2) < O(2^n) < O(n!)$



