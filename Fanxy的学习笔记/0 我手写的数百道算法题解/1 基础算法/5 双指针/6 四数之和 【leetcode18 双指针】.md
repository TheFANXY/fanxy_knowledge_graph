# <font color='bb000'>四数之和【leetcode18 双指针】</font>


## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。请你找出并返回满足下述全部条件且**不重复**的四元组 `[nums[a], nums[b], nums[c], nums[d]]` （若两个四元组元素一一对应，则认为两个四元组重复）：

- `0 <= a, b, c, d < n`
- `a`、`b`、`c` 和 `d` **互不相同**
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

你可以按 **任意顺序** 返回答案 。

 

**示例 1：**

```
输入：nums = [1,0,-1,0,-2,2], target = 0
输出：[[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
```

**示例 2：**

```
输入：nums = [2,2,2,2,2], target = 8
输出：[[2,2,2,2]]
```

 

**提示：**

- `1 <= nums.length <= 200`
- `-109 <= nums[i] <= 109`
- `-109 <= target <= 109`





### 解析 这道题是如下两道题的升级版

### [我的三数之和题解](https://www.acwing.com/solution/content/200361/)

### [我的最接近的三数之和题解](https://www.acwing.com/solution/content/200458/)

首先，我们要分析一点，最暴力的情况就是四重嵌套，判求和，而**为了排除重复，可以先对数组排序，内部规定 `i < j < k < l`，同时满足指针移动的时候，防止出现和前一个位置相同的元素。**

一般双指针算法，都是找到了单调性，才能耦合，把 `O(n ^ 2) 转化为 O(n)` ，这道题，**我们排序之后，再进行枚举，为了找到一个刚好求和最接近 `target` 的数，在外部的 `i 和 j`双重循环嵌套的情况下，我们使用双指针，让 `k` 从 `j + 1` 开始， `l` 从 `nums.length - 1` 开始，进行双指针算法。维护一个临界值，即当 `i` 和 `j` 和 `k` 确定之后，刚好四数之和满足大于等于 `target`，且 `nums[l]` 在右移的过程中最小的情况。而如果刚好 四数之和和 `target` 相等 ，就加入答案中**

**那么这道题不同的是，首先没有仔细看数据的范围，前两道题的数据保证相加很小，但是这道题每个数的范围最大为 `1e9`，那么求和是有爆`int`的可能的，有一个样例就是如此，相加为爆 `int` 之后的负数，故我们求和的数需要类型提升为 `long`，防止出现越界然后错判为求和成功**

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> ans = new ArrayList<List<Integer>>();
        int n = nums.length;
        if (n < 4) return ans;
        Arrays.sort(nums);

        for (int i = 0; i < n; i ++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            for (int j = i + 1; j < n; j ++) {
                if (j - 1 > i && nums[j] == nums[j - 1]) continue;
                for (int k = j + 1, l = n - 1; k < l; k ++) {
                    if (k - 1 > j && nums[k] == nums[k - 1]) continue;
                    while (l - 1 > k && (long) nums[i] + nums[j] + nums[k] + nums[l - 1] >= target) l --;
                    if ((long) nums[i] + nums[j] + nums[k] + nums[l] == target) 
                        ans.add(Arrays.asList(nums[i], nums[j], nums[k], nums[l]));
                }
            }
        }
        return ans;
    }
}
```

