# Jewels and Stones
## Description
You're given strings J representing the types of stones that are jewels, and S representing the stones you have.  Each character in S is a type of stone you have.  You want to know how many of the stones you have are also jewels.

The letters in J are guaranteed distinct, and all characters in J and S are letters. Letters are case sensitive, so "a" is considered a different type of stone from "A".

### Example
Example 1:
```
Input: J = "aA", S = "aAAbbbb"
Output: 3
```
Example 2:
```
Input: J = "z", S = "ZZ"
Output: 0
```
## Solution
```java
class Solution {
    public int numJewelsInStones(String J, String S) {
        Set<Character> set = new HashSet();//Space complexity O(n)
        int count = 0;
        for(char c : J.toCharArray()){ //O(n)
            set.add(c);
        }
        for(char d : S.toCharArray()){ //O(n)
            if(set.contains(d))
                count++;
        }
        return count;
    
    }
}
```