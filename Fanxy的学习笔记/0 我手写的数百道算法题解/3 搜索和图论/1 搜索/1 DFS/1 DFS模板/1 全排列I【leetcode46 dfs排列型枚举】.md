# <font color='bb000'>全排列I【leetcode46 dfs排列型枚举】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给定一个不含重复数字的数组 `nums` ，返回其 *所有可能的全排列* 。你可以 **按任意顺序** 返回答案。

 

**示例 1：**

```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**示例 2：**

```
输入：nums = [0,1]
输出：[[0,1],[1,0]]
```

**示例 3：**

```
输入：nums = [1]
输出：[[1]]
```



**提示：**

- `1 <= nums.length <= 6`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有整数 **互不相同**



### 解析

不太适应 `leetcode` 这种非 `acm` 赛制的，自己每次开个静态变量，都得考虑半天要不要重置。

```java
class Solution {
    static List<List<Integer>> ans = new ArrayList<>();
    static List<Integer> path = new ArrayList<>();
    static boolean [] st;

    public void dfs(int nums[], int index) {
        if (index == nums.length) {
            ans.add(new ArrayList(path));
            return;
        }
        for (int i = 0; i < nums.length; i ++) {
            if (!st[i]) {
                st[i] = true;
                path.add(nums[i]);
                dfs(nums, index + 1);
                st[i] = false;
                path.remove(path.size() - 1);
            }
        }
    }

    public List<List<Integer>> permute(int[] nums) {
        ans.clear();
        int n = nums.length;
        st = new boolean[n + 2];
        dfs(nums, 0);
        return ans;
    }
}
```



