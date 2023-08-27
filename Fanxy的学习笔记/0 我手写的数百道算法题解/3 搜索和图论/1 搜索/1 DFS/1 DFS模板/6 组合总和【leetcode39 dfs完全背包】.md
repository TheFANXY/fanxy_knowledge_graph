# <font color="bb000">组合总和【leetcode39 dfs完全背包】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你一个 **无重复元素** 的整数数组 `candidates` 和一个目标整数 `target` ，找出 `candidates` 中可以使数字和为目标数 `target` 的 所有 **不同组合** ，并以列表形式返回。你可以按 **任意顺序** 返回这些组合。

`candidates` 中的 **同一个** 数字可以 **无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

对于给定的输入，保证和为 `target` 的不同组合数少于 `150` 个。

 

**示例 1：**

```
输入：candidates = [2,3,6,7], target = 7
输出：[[2,2,3],[7]]
解释：
2 和 3 可以形成一组候选，2 + 2 + 3 = 7 。注意 2 可以使用多次。
7 也是一个候选， 7 = 7 。
仅有这两种组合。
```

**示例 2：**

```
输入: candidates = [2,3,5], target = 8
输出: [[2,2,2,2],[2,3,3],[3,5]]
```

**示例 3：**

```
输入: candidates = [2], target = 1
输出: []
```

 

**提示：**

- `1 <= candidates.length <= 30`
- `2 <= candidates[i] <= 40`
- `candidates` 的所有元素 **互不相同**
- `1 <= target <= 40`



### 解析

这道题初看，会觉得应该是完全背包，但是求具体方案，如果完全背包，本身复杂度就是 O(n * m) ，加上再搜索方案，不如直接爆搜。

这里需要注意的点就是，给一个 `List<List>` 新增一个 `List` 需要使用 `new`  .

```java
class Solution {
    
    static List<List<Integer>> ans = new ArrayList<>();

    static void dfs(int [] nums, int index, int target, List<Integer> list) {
        if (target == 0) {
            ans.add(new ArrayList<Integer>(list));
            return;
        }
        if (index == nums.length || target < 0) return;

        for (int i = 0; i * nums[index] <= target; i ++) {
            if (i != 0) list.add(nums[index]);
            dfs(nums, index + 1, target - i * nums[index], list);
        }
        // 恢复现场
        for (int i = 0; i * nums[index] <= target; i ++)
            if (i != 0) list.remove(list.size() - 1);
    }
    
    public List<List<Integer>> combinationSum(int[] nums, int target) {
        ans.clear();
        dfs(nums, 0, target, new ArrayList<Integer>());
        return ans;
    }
}
```

