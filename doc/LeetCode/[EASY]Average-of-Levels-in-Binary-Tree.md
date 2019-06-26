# Average of Levels in Binary Tree
## Description
Given a non-empty binary tree, return the average value of the nodes on each level in the form of an array.

## Solution
```java
    public List<Double> averageOfLevels(TreeNode root) {
        List<Double> result = new ArrayList<>();
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            int size = q.size();
            double levelSum = 0;
            for (int i = 0; i < size; i++) {
                TreeNode poll = q.poll();
                levelSum += poll.val;
                if (poll.left != null) q.offer(poll.left);
                if (poll.right != null) q.offer(poll.right);
            }
            result.add(levelSum / size);
        }
        return result;
    }
```