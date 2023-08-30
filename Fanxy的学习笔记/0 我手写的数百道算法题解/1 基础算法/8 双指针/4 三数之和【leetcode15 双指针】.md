# <font color='bb000'>三数之和【leetcode15 双指针】</font>


## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请

你返回所有和为 `0` 且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

 

**示例 1：**

```java
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
解释：
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 。
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 。
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 。
不同的三元组是 [-1,0,1] 和 [-1,-1,2] 。
注意，输出的顺序和三元组的顺序并不重要。
```

**示例 2：**

```java
输入：nums = [0,1,1]
输出：[]
解释：唯一可能的三元组和不为 0 。
```

**示例 3：**

```java
输入：nums = [0,0,0]
输出：[[0,0,0]]
解释：唯一可能的三元组和为 0 。
```

 

**提示：**

- `3 <= nums.length <= 3000`
- `-10^5 <= nums[i] <= 10^5`



### 解析

首先，我们要分析一点，最暴力的情况就是三重嵌套，判求和，而**为了排除重复，可以先对数组排序，内部规定 `i < j < k`，同时满足前两个指针移动的时候，防止出现和前一个位置相同的元素。**

一般双指针算法，都是找到了单调性，才能耦合，把 `O(n ^ 2) 转化为 O(n)` ，这道题，**我们排序之后，再进行枚举，为了找到一个刚好求和为 0 的数，我们使用双指针，让 `j` 从 `i + 1` 开始， `k` 从 `nums.length - 1` 开始，进行双指针算法。维护一个临界值，即当 `i` 和 `j` 确定之后，刚好三数之和满足大于等于0，且 `nums[k]` 在右移的过程中最小的情况。而如果刚好为 0 ，结合上面的去重思想，就可以把一个唯一的答案放入答案数组**。

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        if (nums.length < 3) return res;

        Arrays.sort(nums);
        for (int i = 0; i < nums.length; i ++) {
            if (i > 0 && nums[i] == nums[i-1]) continue;
            for (int j = i + 1, k = nums.length - 1; j < k; j ++) {
                if (j > i + 1 && nums[j] == nums[j - 1]) continue;
                while (k - 1 > j && nums[i] + nums[j] + nums[k - 1] >= 0) k --;
                if (nums[i] + nums[j] + nums[k] == 0) {
                    res.add(Arrays.asList(nums[i], nums[j], nums[k]));
                }
            }
        }
        return res;
    }
}
```

