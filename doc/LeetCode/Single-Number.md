## Description
Given an array of integers, every element appears twice except for one. Find that single one.

## Solution
```java
public int singleNumber(int[] nums) {
    int result = 0;
    for(int i : nums) {
        result ^= i;
    }
    return result;
}
```
采用异或运算，运用其两数相同为0的特性，0^num=num

异或运算没有先后顺序，可以理解成加法的形式。