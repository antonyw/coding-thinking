# N-ary Tree Preorder Traversal
## Description
Given an n-ary tree, return the preorder traversal of its nodes' values.

For example, given a 3-ary tree:
![](../../img/narytreeexample.png)
Return its preorder traversal as: [1,3,5,6,2,4].

## Solution
```java
class Solution {
    public List<Integer> preorder(Node root) {
        List<Integer> result = new ArrayList<Integer>();
        dfs(root, result);
        return result;
    }
    private void dfs(Node node, List<Integer> result) {
        if (node == null) return;
        result.add(node.val);
        for (Node child : node.children) {
            dfs(child, result);
        }
    }
}
```