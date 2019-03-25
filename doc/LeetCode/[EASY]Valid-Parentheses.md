# Valid Parentheses

## Description
Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.

Note that an empty string is also considered valid.

### Example
Example 1:
```
Input:"()"
Output:true
```
Example 2:
```
Input: "()[]{}"
Output: true
```
Example 3:
```
Input: "(]"
Output: false
```
Example 4:
```
Input: "([)]"
Output: false
```
Example 5:
```
Input: "{[]}"
Output: true
```
## Solution
```java
class Solution {
    public boolean isValid(String s) {
        if (s.length()==0)
            return true;
        if (s == null || s.length() % 2 == 1 || !isLeft(s.charAt(0))) {
            return false;
        }
        char[] chars = s.toCharArray();
        Stack<Character> stack = new Stack<>();
        stack.push(chars[0]);
        for (int i = 1; i < chars.length; i++) {
            if (!isLeft(chars[i])) {
                if (stack.size() == 0)
                    return false;
                Character last = stack.peek();
                if (isVaild(last, chars[i])) {
                    stack.pop();
                } else {
                    return false;
                }
            } else {
                stack.push(chars[i]);
            }
        }
        return stack.size() == 0;
    }

    private boolean isLeft(char c) {
        return c == '{' || c == '(' || c == '[';
    }

    private boolean isVaild(char left, char right) {
        if (left < 90) // '('=40 ')'=41 '['=91 ']'=93 '{'=123 '}'=125
            return left + 1 == right;
        else
            return left + 2 == right;
    }
}
```