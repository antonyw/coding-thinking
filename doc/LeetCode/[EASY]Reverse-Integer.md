# Reverse Integer
## Description
Reverse digits of an integer.

### Example
Example1: 
```
x = 123, return 321
```

Example2: 
```
x = -123, return -321
```

Note:
The input is assumed to be a 32-bit signed integer. Your function should return 0 when the reversed integer overflows.
## Solution
```java
public class Solution {
    public int reverse(int x) {
        int result = 0;
        while (x != 0){
            int tail = x % 10;
            int newResult = result * 10 + tail;
            if ((newResult - tail) / 10 != result)
            { return 0; }
            result = newResult;
            x = x / 10;
        }

        return result;
    }
   
}
```