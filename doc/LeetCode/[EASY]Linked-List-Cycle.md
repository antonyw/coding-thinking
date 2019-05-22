# Linked List Cycle
## Description
Given a linked list, determine if it has a cycle in it.

To represent a cycle in the given linked list, we use an integer pos which represents the position (0-indexed) in the linked list where tail connects to. If pos is -1, then there is no cycle in the linked list.

### Example
Example 1:
```
Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where tail connects to the second node.
```

Example 2:
```
Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where tail connects to the first node.
```

## Solution
```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 * 快慢指针
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head==null) return false;
        ListNode left = head, right = head.next;
        while (right != null && right.next != null) {
            if (left == right) {
                return true;
            } else {
                left = left.next;
                right = right.next.next;
            }
        }
        return false;
    }
}
```