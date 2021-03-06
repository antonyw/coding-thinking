# Longest Continuous Increasing Subsequence
## Description
Given an unsorted array of integers, find the length of longest continuous increasing subsequence (subarray). 

### Example
Example 1 :
```
Input: [1,3,5,4,7]
Output: 3
Explanation: The longest continuous increasing subsequence is [1,3,5], its length is 3. 
Even though [1,3,5,7] is also an increasing subsequence, it's not a continuous one where 5 and 7 are separated by 4. 
```

Example 2 :
```
Input: [2,2,2,2,2]
Output: 1
Explanation: The longest continuous increasing subsequence is [2], its length is 1. 
```

Note :Length of the array will not exceed 10,000. 

## Solution
```java
/**
* Runtime: 2 ms
* Beats 97.82% of java submissions at 03-23-2019
**/
class Solution {
    public int findLengthOfLCIS(int[] nums) {
        if (nums.length < 2) return nums.length;
        int result = 1;
        int tempRes = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] > nums[i - 1]) {
                tempRes++;
            } else {
                result = Math.max(result, tempRes);
                tempRes = 1;
            }
        }
        return result == tempRes ? result : (Math.max(result, tempRes));
    }
}
```