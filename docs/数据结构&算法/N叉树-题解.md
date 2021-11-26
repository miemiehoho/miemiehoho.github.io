#### [589. N 叉树的前序遍历](https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/)

```java
// 递归
List<Integer> resList = new ArrayList<>();

public List<Integer> preorder(Node root) {
    if (root == null) {
        return resList;
    }
    resList.add(root.val);
    for (Node node : root.children) {
        preorder(node);
    }
    return resList;
}
```

```java
// 迭代
public List<Integer> preorder(Node root) {
    List<Integer> resList = new ArrayList<>();
    if (root == null) {
        return resList;
    }
    Deque<Node> stack = new LinkedList<>();
    stack.offerLast(root);
    while (!stack.isEmpty()) {
        root = stack.pollLast();
        if (root != null) {
            resList.add(root.val);
            List<Node> children = root.children;
            for (int i = children.size() - 1; i >= 0; i--) {
                stack.offerLast(children.get(i));
            }
        }
    }
    return resList;
}
```

#### [590. N 叉树的后序遍历](https://leetcode-cn.com/problems/n-ary-tree-postorder-traversal/)

```java
// 递归
List<Integer> resList = new ArrayList<>();

public List<Integer> postorder(Node root) {
    if (root == null) {
        return resList;
    }
    for (Node node : root.children) {
        postorder(node);
    }
    resList.add(root.val);
    return resList;
}
```

```java
// 迭代
public List<Integer> postorder(Node root) {
    List<Integer> resList = new ArrayList<>();
    if (root == null) {
        return resList;
    }
    Deque<Node> stack = new LinkedList<>();
    stack.offerLast(root);
    while (!stack.isEmpty()) {
        root = stack.pollLast();
        if (root != null) {
            resList.add(root.val);
            List<Node> children = root.children;
            for (int i = 0; i < children.size(); i++) {
                stack.offerLast(children.get(i));
            }
        }
    }
    Collections.reverse(resList);
    return resList;
}
```

#### [429. N 叉树的层序遍历](https://leetcode-cn.com/problems/n-ary-tree-level-order-traversal/)

```java
// BFS
public List<List<Integer>> levelOrder(Node root) {
    List<List<Integer>> resList = new ArrayList<>();
    if (root == null) {
        return resList;
    }
    Queue<Node> queue = new LinkedList<>();
    queue.offer(root);
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> list = new ArrayList<>();
        while (size-- > 0) {
            root = queue.poll();
            if (root != null) {
                list.add(root.val);
                for (Node node : root.children) {
                    queue.offer(node);
                }
            }
        }
        resList.add(list);
    }
    return resList;
}
```

#### [559. N 叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-n-ary-tree/)

```java
// 递归
public int maxDepth(Node root) {
    if (root == null) {
        return 0;
    }
    int maxDepth = 0;
    for (Node node : root.children) {
        int depth = maxDepth(node);
        maxDepth = Math.max(maxDepth, depth);
    }
    return maxDepth + 1;
}
```

##### ⭐迭代

```java
// 迭代
public int maxDepth(Node root) {
    if (root == null) {
        return 0;
    }
    Queue<Node> queue = new LinkedList<>();
    queue.offer(root);
    int level = 0;
    while (!queue.isEmpty()) {
        int size = queue.size();
        while (size-- > 0) {
            root = queue.poll();
            for (Node node : root.children) {
                if (node != null) {
                    queue.offer(node);
                }
            }
        }
        level++;
    }
    return level;
}
```

#### [428. 序列化和反序列化 N 叉树](https://leetcode-cn.com/problems/serialize-and-deserialize-n-ary-tree/)

```java
// 层序遍历	
class Codec {
    // Encodes a tree to a single string.
    public String serialize(Node root) {
        if (root == null) {
            return "";
        }
        StringBuffer sb = new StringBuffer();
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);
        sb.append(root.val + "," + "N,");
        while (!queue.isEmpty()) {
            int size = queue.size();
            while (size-- > 0) {
                root = queue.poll();
                List<Node> children = root.children;
                if (children != null) {
                    for (Node node : children) {
                        queue.offer(node);
                        sb.append(node.val + ",");
                    }
                }
                // 分组
                sb.append("N,");
            }
        }
        return sb.toString();
    }

    // Decodes your encoded data to tree.
    public Node deserialize(String data) {
        if ("".equals(data)) {
            return null;
        }
        String[] strings = data.split(",");
        Queue<Node> queue = new LinkedList<>();
        Node root = new Node();
        root.val = Integer.valueOf(strings[0]);
        queue.offer(root);
        int i = 2;
        while (!queue.isEmpty()) {
            int size = queue.size();
            while (size-- > 0) {
                Node cur = queue.poll();
                // 有子节点
                List<Node> children = new ArrayList<>();
                if (!"N".equals(strings[i])) {
                    while (!"N".equals(strings[i])) {
                        Node node = new Node();
                        node.val = Integer.valueOf(strings[i++]);
                        children.add(node);
                        queue.offer(node);
                    }
                }
                cur.children = children;
                i++;
            }
        }
        return root;
    }
}
```