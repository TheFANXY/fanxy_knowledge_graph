# <font color='red'>数的范围【二分】（边界条件分析）</font>

## **`我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

# 题目介绍

给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

你必须设计并实现时间复杂度为 `O(log n)` 的算法解决此问题。

 

**示例 1：**

```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

**示例 2：**

```
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
```

**示例 3：**

```
输入：nums = [], target = 0
输出：[-1,-1]
```

 

**提示：**

- `0 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`
- `nums` 是一个非递减数组
- `-10^9 <= target <= 10^9`

# 边界条件分析

当我们二分判断的条件是**`R = mid`**的情况下，mid的赋值就不需要加一：**`l + r >> 1`**；
因为边界条件是当**`l = r - 1`**即相邻的情况下 这么赋值根据整除的性质 r会变成l跳出**`while （l < r)`**



### 区别

当我们二分判断的条件是**`L = mid`**的情况下，mid的赋值就需要加一：**`l + r + 1 >> 1`**；
因为边界条件是当**`l = r - 1`**即相邻的情况下 这么赋值根据整除的性质 l会变成r跳出**`while （l < r)`**



# java 代码

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int l = 0, r = nums.length - 1;
        int [] a = new int [2];
        a[0] = a[1] = -1;

        if (nums.length == 0) return a;
        while (l < r) {
            int mid = l + r >> 1;
            if (nums[mid] < target) l = mid + 1;
            else r = mid;
        }
        if (nums[l] != target) return a;
        a[0] = l;
        r = nums.length - 1;
        while (l < r) {
            int mid = l + r + 1 >> 1;
            if (nums[mid] > target) r = mid - 1;
            else l = mid;
        }
        a[1] = l;
        return a;
    }
}
```

