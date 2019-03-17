# Sum of Left Leaves
## Description
Find the sum of all left leaves in a given binary tree.

### Example
<br>
&nbsp;&nbsp;&nbsp;&nbsp;3 <br>
&nbsp;&nbsp;&nbsp;/&nbsp;&nbsp;\ <br>
&nbsp;&nbsp;9&nbsp;&nbsp;20 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;/&nbsp;&nbsp;\ <br>
&nbsp;&nbsp;&nbsp;&nbsp;15&nbsp;&nbsp;7 <br>

There are two left leaves in the binary tree, with values 9 and 15 respectively. Return 24.

## Solution
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        int r = 0;
        if (root == null) return r; 
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode pop = stack.pop();
            if (pop.left != null) {
                if (pop.left.left == null && pop.left.right == null) {
                    r += pop.left.val;
                } else
                    stack.push(pop.left);
            }
            if (pop.right != null) {
                stack.push(pop.right);
            }
        }
        return r;
    }
}
```