# <font color="bb000">两数之和 【leetcode1 双指针判元素个数】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** *`target`* 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

 

**示例 1：**

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

**示例 2：**

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

**示例 3：**

```
输入：nums = [3,3], target = 6
输出：[0,1]
```

 

**提示：**

- `2 <= nums.length <= 104`
- `-109 <= nums[i] <= 109`
- `-109 <= target <= 109`
- **只会存在一个有效答案**

 

**进阶：**你可以想出一个时间复杂度小于 `O(n2)` 的算法吗？



## 解析

显然这道题正常不思考都知道可以嵌套循环变成暴力`O(n ^ 2)`，只是找两数之和，且没有单调关系就没必要用双指针，可以直接用哈希表，因为要返回最后两个数的数组，我们直接把数当成 `Key` ，然后下标作为 `Value`，枚举每个数，都进行判断互补数在不在哈希表里面，同时把当前数插入即可。

数据保证有解，这里为了不报错直接抛异常即可优化速率。

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int i=0; i < nums.length; i++){
            if (map.containsKey(target - nums[i])) 
                return new int[]{i, map.get(target - nums[i])};
            map.put(nums[i], i);
        }
        throw new IllegalArgumentException("No answers");
    }
}
```



