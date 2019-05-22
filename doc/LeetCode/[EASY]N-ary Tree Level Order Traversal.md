# N-ary Tree Level Order Traversal
## Description
Given an n-ary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).

For example, given a 3-ary tree:
![](../../img/narytreeexample.png)

We should return its level order traversal:
```
[
     [1],
     [3,2,4],
     [5,6]
]
```

## Solution
```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}

    public Node(int _val,List<Node> _children) {
        val = _val;
        children = _children;
    }
};
*/
class Solution {
    public List<List<Integer>> levelOrder(Node root) {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) return result;
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            List<Integer> list = new ArrayList<>();
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                Node poll = queue.poll();
                if (poll == null) continue;
                list.add(poll.val);
                if (poll.children != null && poll.children.size() > 0) {
                    for (Node child : poll.children) {
                        queue.offer(child);
                    }
                }
            }
            result.add(list);
        }
        return result;
    }
}
```