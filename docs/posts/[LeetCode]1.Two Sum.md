---
title: LeetCode-1.Two Sum
tag: LeetCode
---
LeetCode 1.Two Sum - Solution

<!-- more -->

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

**Example:**

```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

**Solution 1**
最简单的办法就是两层循环遍历数组计算两个数的和，如果找到则记录两个数的index，如果未找到则返回null。下面是代码：

```
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] result = new int[2];
        for(int i = 0; i < nums.length - 1; i++){
            for(int j = i + 1; j < nums.length; j++){
                if(nums[i] + nums[j] == targer){
                    result[0] = i;
                    result[1] = j;
                }
            }
        }
        return null;
    }
}
```

**Solution 2**
自己能想到的解决办法就是上面这种，ac以后看了下耗时最少的代码，结果还有下面这种一层循环的解决办法。使用Map记录已出现过的数以及它的index，每遍历一个数num，看map中是否存在target-num，如果有就返回两个数的index即可。如果不存在，则将当前遍历的数以及它的index存入map。代码如下：
```
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] result = new int[2];
        Map<Integer,Integer> map = new HashMap<Integer,Integer>();
        if(nums != null && nums.length >= 2){
            for(int i = 0; i < nums.length; i++){
                int diffValue = target - nums[i];
                if(map.containsKey(diffValue)){
                    result[0] = map.get(diffValue);
                    result[1] = i;
                    return result;
                }
                map.put(nums[i],i);
            }
        }
        return null;
    }
}
```

虽然是很简单的问题，却有更好的处理办法，这就是算法的追求所在吧。不过一般搞算法的也不会纠结在这种问题吧。另外，第二种方法使用了Map会不会影响效率？