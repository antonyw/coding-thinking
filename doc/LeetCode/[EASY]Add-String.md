# Add String
## Description
Given two non-negative integers num1 and num2 represented as string, return the sum of num1 and num2.

### Note:
1. The length of both num1 and num2 is < 5100.
2. Both num1 and num2 contains only digits 0-9.
3. Both num1 and num2 does not contain any leading zero.
4. You must not use any built-in BigInteger library or convert the inputs to integer directly.

## Solution
```java
class Solution {
    public String addStrings(String num1, String num2) {
        int carry = 0;
        char[] array1 = num1.toCharArray();
        char[] array2 = num2.toCharArray();
        int i = num1.length() - 1, y = num2.length() - 1;
        StringBuilder builder = new StringBuilder();
        while (i >= 0 || y >= 0) {
            int x1 = 0, x2 = 0;
            if (i >= 0) x1 = Character.getNumericValue(array1[i--]);
            if (y >= 0) x2 = Character.getNumericValue(array2[y--]);
            int s = x1 + x2 + carry;
            if (s > 9) {
                carry = 1;
                int l = s % 10;
                builder.append(l);
            } else {
                carry = 0;
                builder.append(s);
            }
        }
        if (carry > 0) {
            builder.append(1);
        }
        return builder.reverse().toString();
    }
}
```
carry相当于“进一位”，每次加carry，并给carry重新赋值。

StringBulider 有更好的拼接效率，还有反转方法。