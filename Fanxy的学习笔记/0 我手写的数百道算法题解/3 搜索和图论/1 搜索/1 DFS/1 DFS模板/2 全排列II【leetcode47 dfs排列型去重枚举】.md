# <font color='bb000'>全排列II【leetcode46 dfs排列型枚举】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给定一个不含重复数字的数组 `nums` ，返回其 *所有可能的全排列* 。你可以 **按任意顺序** 返回答案。

 

**示例 1：**

```java
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**示例 2：**

```java
输入：nums = [0,1]
输出：[[0,1],[1,0]]
```

**示例 3：**

```java
输入：nums = [1]
输出：[[1]]
```

 

**提示：**

- `1 <= nums.length <= 6`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有整数 **互不相同**



### 解析

这次给我们的数组出现了重复元素，我们的答案需要不能出现重复全排列。

这个题，结合前面的三数之和，四数之和，我们可以思考到，我们可以排序，如果上一个数和现在的数相等，就直接枚举下一个数。

但是三数之和那道题，保证了把所有的情况枚举，不需要使用 `st` 数组判断下一位用什么。

所以我们跳出的前提，应该是上一个数没被用到的情况下，不能单纯只靠相等就跳出，要不然就出现一个数被用了第一次，本身下一个相同的数如果顺序对的话，还能用，但是不进行 `st` 判断，相同的数就只能用一次了。

```java
class Solution {   
    static List<List<Integer>> ans = new ArrayList<>();
    static List<Integer> path = new ArrayList<>();
    static boolean [] st;

    public void dfs(int [] nums, int index) {
        if (index == nums.length) {
            ans.add(new ArrayList(path));
            return;
        }
        for (int i = 0; i < nums.length; i ++) {
            if (!st[i]) {
                if (i != 0 && nums[i - 1] == nums[i] && !st[i - 1]) continue;
                st[i] = true;
                path.add(nums[i]);
                dfs(nums, index + 1);
                st[i] = false;
                path.remove(path.size() - 1);
            }
        }
    }

    public List<List<Integer>> permuteUnique(int[] nums) {
        Arrays.sort(nums);
        ans.clear();
        st = new boolean[nums.length + 2];
        dfs(nums, 0);
        return ans;
    }
}
```



