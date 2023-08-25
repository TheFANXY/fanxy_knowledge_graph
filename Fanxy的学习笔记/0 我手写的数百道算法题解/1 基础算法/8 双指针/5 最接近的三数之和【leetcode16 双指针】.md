# <font color='bb000'>最接近的三数之和【leetcode16 双指针】</font>


## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你一个长度为 `n` 的整数数组 `nums` 和 一个目标值 `target`。请你从 `nums` 中选出三个整数，使它们的和与 `target` 最接近。

返回这三个数的和。

**假定每组输入只存在恰好一个解。**

 

**示例 1：**

```
输入：nums = [-1,2,1,-4], target = 1
输出：2
解释：与 target 最接近的和是 2 (-1 + 2 + 1 = 2) 。
```

**示例 2：**

```
输入：nums = [0,0,0], target = 1
输出：0
```

 

**提示：**

- `3 <= nums.length <= 1000`
- `-1000 <= nums[i] <= 1000`
- `-104 <= target <= 104`



### 解析

### 这道题是上一道题的翻版[我的三数之和题解](https://www.acwing.com/solution/content/200361/)

首先，我们要分析一点，最暴力的情况就是三重嵌套，判求和，而**为了排除重复【这道题答案唯一，但是判重操作可以把重复的状态排除防止重复判断】，可以先对数组排序，内部规定 `i < j < k`，同时满足前两个指针移动的时候，防止出现和前一个位置相同的元素。**

一般双指针算法，都是找到了单调性，才能耦合，把 `O(n ^ 2) 转化为 O(n)` ，这道题，**我们排序之后，再进行枚举，为了找到一个刚好求和最接近 `target` 的数，我们使用双指针，让 `j` 从 `i + 1` 开始， `k` 从 `nums.length - 1` 开始，进行双指针算法。维护一个临界值，即当 `i` 和 `j` 确定之后，刚好三数之和满足大于等于 `target`，且 `nums[k]` 在右移的过程中最小的情况。而如果刚好 三数之和和 `target` 相等 ，无需后续判断，因为有唯一解，直接返回答案。如果找到当前组合的最小 `nums[k]` ，可以确定三数之和大于等于 `target`，无需进行绝对值，直接 `s - target`，判断和当前答案和 `target` 差值【abs】谁最小，小于就更新。**

**那么这道题不同的是，有可能答案是小于 `target` 的情况 ，而我们的操作，只能找到比它大的数，很简单，我们每次找到刚好满足 `s >= target` 的一组解，只需要 `k - 1` ，即能找到当前确定的 `i 和 j` 小于 `target` 的最接近的一组解，然后也不需要进行绝对值，直接 `target - s` 判断当前答案和 `target` 差值【abs】谁最小，小于就更新。**

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        int n = nums.length;
        int ans = 0x3f3f3f3f;
        Arrays.sort(nums);

        for (int i = 0; i < n; i ++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            for (int j = i + 1, k = n - 1; j < k; j ++) {
                if (j - 1 > i && nums[j] == nums[j - 1]) continue;
                while (k - 1 > j && nums[i] + nums[j] + nums[k - 1] >= target) k --;
                if (nums[i] + nums[j] + nums[k] == target) return target;
                int s = nums[i] + nums[j] + nums[k];
                if (Math.abs(s - target) < Math.abs(ans - target)) ans = s;
                if (k - 1 > j) {
                    s = nums[i] + nums[j] + nums[k - 1];
                    if ((target - s) < Math.abs(ans - target)) ans = s;
                } 
            }
        }
        return ans;
    }
}
```

