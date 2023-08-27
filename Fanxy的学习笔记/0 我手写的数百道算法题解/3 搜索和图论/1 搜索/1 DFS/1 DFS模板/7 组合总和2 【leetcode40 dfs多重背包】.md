# <font color="bb000">组合总和2 【leetcode40 dfs多重背包】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给定一个候选人编号的集合 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。

`candidates` 中的每个数字在每个组合中只能使用 **一次** 。

**注意：**解集不能包含重复的组合。 

 

**示例 1:**

```apl
输入: candidates = [10,1,2,7,6,1,5], target = 8,
输出:
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```

**示例 2:**

```apl
输入: candidates = [2,5,2,1,2], target = 5,
输出:
[
[1,2,2],
[5]
]
```

 

**提示:**

- `1 <= candidates.length <= 100`
- `1 <= candidates[i] <= 50`
- `1 <= target <= 30`





### 解析

这道题和上一题的区别就是，每个数只能使用一次，那么我们可以先对整个数组排序，然后使用双指针，确定相同的数的数量，这样就代表可以最多用多少次这个数，同时 `dfs` 前往下一次的时候，就利用之前的双指针，直接进入下一个不同的数的枚举。

```java
class Solution {
    static List<List<Integer>> ans = new ArrayList<>();
    public void dfs(int [] nums, int index, int target, List<Integer> path) {
        if (target == 0) {
            ans.add(new ArrayList<Integer>(path));
            return;
        }
        if (index == nums.length) return;
        int sentinel = index + 1;
        while (sentinel < nums.length && nums[sentinel] == nums[index]) sentinel ++;
        int cnt = sentinel - index;
        for (int i = 0; i * nums[index] <= target && i <= cnt; i ++) {
            if (i != 0) path.add(nums[index]);
            dfs(nums, sentinel, target - i * nums[index], path);
        }
        for (int i = 0; i * nums[index] <= target && i <= cnt; i ++) {
            if (i != 0) path.remove(path.size() - 1);
        }
    }

    public List<List<Integer>> combinationSum2(int[] nums, int target) {
        ans.clear();
        Arrays.sort(nums);
        dfs(nums, 0, target, new ArrayList<>());
        return ans;
    }
}
```

