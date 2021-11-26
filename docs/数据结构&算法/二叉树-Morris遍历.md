> 左神视频直通车：https://www.bilibili.com/video/BV1tX4y1G715?p=5

## Morris遍历介绍

**Morris遍历应用场景：**

给定⼀棵⼆叉树的头节点head，完成⼆叉树的先序、中序和后序遍历。如果⼆叉树的节点数为N ，要求时间复杂度为O (N )，额外空 间复杂度为O (1)

**解答：**

1. 递归与迭代遍历二叉树的空间复杂度都是O(h)

   这是因为遍历⼆叉树的递归⽅法实际使⽤了函数 栈，非递归的⽅法使⽤了申请的栈，两者的额外空间都与树的⾼度相 关，所以空间复杂度为O (h )，h 为⼆叉树的⾼度

2. 如何完全不⽤栈结构能完成三种遍历：使⽤⼆叉树节点中⼤量指向 null的指针。就是⼤名⿍⿍的Morris遍历

**Morris遍历实质**

Morris遍历的实质就是避免⽤栈结构，⽽是让下层到上层有指针，具体是通过让底层节点指向null的空闲指针指回上层的某个节 点，从⽽完成下层到上层的移动。我们知道，⼆叉树上的很多节点都 有⼤量的空闲指针，⽐如，某些节点没有右孩⼦，那么这个节点的 right指针就指向null，我们称为空闲状态，Morris遍历正是利⽤了这 些空闲指针

## Morris序

Morris序遍历规则：

1. cur 无左树，cur = cur.right
2. cur 有左树，找到左树的最右节点 mostRight
   1. mostRight 的右指针指向 null 的，mostRight.right = cur，cur = cur.left
   2. mostRight 的右指针指向cur的，mostRight.right = null，cur = cur.right

```java
// morris遍历规则：
// 1）cur无左树，cur = cur.right
// 2) cur有左树，找到左树最右节点，mostRight
//   ① mostRight的右指针指向null的，mostRight.right = cur ,cur= cur.left
//   ② mostRight的右指针指向cur，mostRight.right = null,cur = cur.right
public void morris(TreeNode head) {
    if (head == null) {
        return;
    }
    TreeNode cur = head;
    TreeNode mostRight = null;
    while (cur != null) {
        mostRight = cur.left;
        // cur有左树
        if (mostRight != null) {
            // 找到左树真实的最右节点
            while (mostRight.right != null && mostRight.right != cur) {
                mostRight = mostRight.right;
            }
            // 第一次来到cur的左子树的最右节点，将mostRight.right = cur,cur = cur.left
            if (mostRight.right == null) {
                mostRight.right = cur;
                cur = cur.left;
                continue;
                // 第二次来到cur的左子树的最右节点，此时mostRight.right = cur,将mostRight.rigth = null
            } else {
                mostRight.right = null;
            }
        }
        // cur没有左子树或者第二次来到cur的左子树的最右节点，使cur = cur.right
        cur = cur.right;
    }
}
```

## 时间复杂度

时间复杂度：O(N)

证明：

1. cur来到所有节点的次数是N
2.  所有节点遍历所有节点的左树的右边界的代价是 2
3. 所以总代价是O(N)

![image-20211105165613857](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/11/202111051656030.png)

## Morris前序、中序、后序遍历

第一次来到一个节点时候打印：先序，第二次来到一个节点的时候打印：中序；只有一次到达的节点：遇到它就打印

### 先序遍历

第一次来到节点时打印，第二次来到节点时不打印，只有一次到达的节点，直接打印（它没有左子树）

![image-20211105170856416](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/11/202111051708389.png)

```java
// 先序遍历，如果一个节点能到达自己两次（即有左子树），第一次到达时打印
public void morrisPre(TreeNode head) {
    if (head == null) {
        return;
    }
    TreeNode cur = head;
    TreeNode mostRight = null;
    while (cur != null) {
        mostRight = cur.left;
        // 如果 cur 有左子树
        if (mostRight != null) {
            // 找到cur左子树的真实最右节点
            while (mostRight.right != null && mostRight.right != cur) {
                mostRight = mostRight.right;
            }
            // 第一次到达cur左子树的最右节点
            if (mostRight.right == null) {
                // cur 有左子树，第一次到达时打印
                System.out.println(cur.val);
                mostRight.right = cur;
                cur = cur.left;
                ;
                continue;
            } else {
                // 第二次到达cur左子树的最右节点，恢复指针
                mostRight.right = null;
            }
        } else {
            // cur没有左子树，直接打印
            System.out.println(cur.val);
        }
        // cur 没有左子树或者 cur第二次到达cur
        cur = cur.right;
    }
}
```



## 中序遍历

对于能到达自己两次的节点（也就是有左树的节点），第二次到达时打印，只有一次到达的节点，直接打印就是中序遍历

![image-20211105171141270](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/11/202111051711262.png)

```java
// 中序遍历，如果一个节点能到达自己两次（即有左子树），第二次到达时打印
public void morrisIn(TreeNode head) {
    if (head == null) {
        return;
    }
    TreeNode cur = head;
    TreeNode mostRight = null;
    while (cur != null) {
        mostRight = cur.left;
        // cur 有左树
        if (mostRight != null) {
            // 找到cur左树真实的最右节点
            while (mostRight.right != null && mostRight.right != cur) {
                mostRight = mostRight.right;
            }
            // 第一次来到cur的左子树的最右节点，将mostRight.right = cur,cur = cur.left
            if (mostRight.right == null) {
                mostRight.right = cur;
                cur = cur.left;
                continue;
            } else {
                // 第二次来到cur的左子树的最右节点，此时mostRight.right = cur,将mostRight.rigth = null
                mostRight.right = null;
            }
        }
        // cur没有左子树或者第二次来到cur的左子树的最右节点，使cur = cur.right

        // cur没有左子树或者第二次来到cur，打印
        System.out.println(cur.val);
        cur = cur.right;
    }
}
```

### 后序遍历

**打印时机：**

- 能回到自己两次，且第二次回到自己的时候
  - 不是打印cur，**逆序**打印cur的左树的右边界
  - 所有过程完成后单独**逆序**打印整棵树的右边界

![image-20211105174713028](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/11/202111051747066.png)

```java
// 后序遍历：如果一个节点能到达自己两次，第二次到达时逆序打印其左子树的右边界，整个树打印完后，单独逆序打印整棵树的右边界
public void morrisPost(TreeNode head) {
    if (head == null) {
        return;
    }
    TreeNode cur = head;
    TreeNode mostRight = null;
    while (cur != null) {
        mostRight = cur.left;
        // 如果 cur 有左子树
        if (mostRight != null) {
            // 找到cur左子树的真实最右节点
            while (mostRight.right != null && mostRight.right != cur) {
                mostRight = mostRight.right;
            }
            // 第一次到达cur左子树的最右节点
            if (mostRight.right == null) {
                mostRight.right = cur;
                cur = cur.left;
                continue;
            } else {
                // 第二次到达cur左子树的最右节点，恢复指针
                mostRight.right = null;
                // 第二次到达cur，逆序打印其左子树的右边界
                printEdge(cur.left);
            }
        }
        // cur 没有左子树或者 cur第二次到达cur
        cur = cur.right;
    }
    // 整个过程完成后逆序打印整棵树的右边界
    printEdge(head);
}

private void printEdge(TreeNode head){
    head = reverseTree(head);
    TreeNode node = head;
    while (node!=null){
        System.out.println(node.val);
        node = node.right;
    }
    reverseTree(head);
}

// 反转右子树-双指针
private TreeNode reverseTree(TreeNode head) {
    if (head.right == null) {
        return head;
    }
    // 反转右边界
    TreeNode cur = head, prev = null;
    while (cur != null) {
        TreeNode next = cur.right;
        cur.right = prev;
        prev = cur;
        cur = next;
    }
    return prev;
}

// 反转右子树-递归
private TreeNode reverseTree(TreeNode head) {
    if (head == null || head.right == null) {
        return head;
    }
    TreeNode node = reverseTree(head.right);
    head.right.right = head;
    head.right = null;
    return node;
}
```

## Morris遍历拓展

### 判断搜索二叉树

搜索二叉树定义：每个节点中的值必须大于（或等于）其左侧子树中的任何值，但小于（或等于）其右侧子树中的任何值。（即其中序遍历时递增序列）

```java
public boolean morrisIsBinarySearch(TreeNode head) {
    if (head == null) {
        return true;
    }
    TreeNode cur = head;
    TreeNode mostRight = null;
    Integer pre = null;
    while (cur != null) {
        mostRight = cur.left;
        // cur有左树
        if (mostRight != null) {
            // 找到左树真实的最右节点
            while (mostRight.right != null && mostRight.right != cur) {
                mostRight = mostRight.right;
            }
            // 第一次来到cur的左子树的最右节点，将mostRight.right = cur,cur = cur.left
            if (mostRight.right == null) {
                mostRight.right = cur;
                cur = cur.left;
                continue;
                // 第二次来到cur的左子树的最右节点，此时mostRight.right = cur,将mostRight.rigth = null
            } else {
                mostRight.right = null;
            }
        }
        // 中序遍历前一个节点值大于当前节点值，返回false
        if (pre != null && pre > cur.val) {
            return false;
        }
        pre = cur.val;
        // cur没有左子树或者第二次来到cur的左子树的最右节点，使cur = cur.right
        cur = cur.right;
    }
    return true;
}
```

### 返回二叉树叶节点的最小高度

**关键问题：**

1. 如何指定cur节点此时的高度
2. 如果cur是叶节点，更新高度最小值

**情况分析：**

1. 如果cur无左树（即只有一次到达自己）,level++ 
2. 如果cur 有左树，区分cur是第一次来到自己还是第二次来到自己
   1. 如果第一次来到自己，level++，即获得cur的高度
   2. 如果是第二次来到自己
      1. 判断左子树的最右节点是不是叶子节点，是则更新最小高度
      2. cur的高度等于当前高度减去左子树右边界长度： level -=rightEdgeSize
3. 整颗树遍历完成后，判断一下树的右边界的最右节点是不是叶子节点，是的话更新一下最小高度

![image-20211105215752871](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/11/202111052157276.png)

```java
public int morrisMinHeight(TreeNode head) {
    if (head == null) {
        return 0;
    }
    TreeNode cur = head;
    TreeNode mostRight = null;
    Integer minHeight = Integer.MAX_VALUE, curLevel = 0;
    while (cur != null) {
        mostRight = cur.left;
        // cur有左树
        if (mostRight != null) {
            int rightEdgeSize = 1;// 左子树右边界长度
            // 找到左树真实的最右节点
            while (mostRight.right != null && mostRight.right != cur) {
                rightEdgeSize++; // 统计左子树右边界长度
                mostRight = mostRight.right;
            }
            // 第一次来到cur的左子树的最右节点，将mostRight.right = cur,cur = cur.left
            if (mostRight.right == null) {
                curLevel++; // 第一次回到cur，curLevel++ 获得cur的高度
                mostRight.right = cur;
                cur = cur.left;
                continue;
            } else {    // 第二次来到cur的左子树的最右节点，此时mostRight.right = cur,将mostRight.rigth = null
                if (mostRight.left == null) {   // 左子树最右节点是叶子节点，更新最小高度
                    minHeight = Math.min(minHeight, curLevel);
                }
                curLevel -= rightEdgeSize; // 左子树最右节点高度减去左子树右边界长度即为cur节点高度
                mostRight.right = null;
            }
        } else {
            // cur 没有左子树，curLevel++ 获得当前高度
            curLevel++;
        }
        // cur没有左子树或者第二次来到cur的左子树的最右节点，使cur = cur.right
        cur = cur.right;
    }
    // 整棵树遍历完后判断树的右边界最右节点是否叶子节点，更新最小高度
    cur = head;
    curLevel = 1;
    // 找到最右节点
    while (cur.right != null) {
        cur = cur.right;
        curLevel++;
    }
    // 是叶子节点则更新最小高度
    if (cur.left == null) {
        minHeight = Math.min(minHeight, curLevel);
    }
    return minHeight;
}
```

## 总结

Morris遍历适用情况：

当既需要当前节点的左子树提供信息，又需要当前节点的右子树提供信息，做整合，则没有办法使用Morris遍历(因为既需要左子树提供信息又需要右子树提供信息)，空间一定不是O(1)

如果求解流程不是递归的不需要左右子树同时提供信息，则可以用Morris遍历

也就是说对于需要收集完全信息才能解决的题目就不能用Morris遍历

对于可以往下推理的，不需要保留之前信息的题目，可以用Morris遍历



### 算法常数复杂度

对于一个给定范围的数据，比如是 10 0000 的，遇到要用哈希表的题目，一定要用数组结构替代哈希表，（虽然哈希表增删改查是O(1),但其实它的常数项是很大的）因为哈希表调用离散函数，这个离散化函数的常数项是很高的，没有自己用数组代替哈希表速度快 

