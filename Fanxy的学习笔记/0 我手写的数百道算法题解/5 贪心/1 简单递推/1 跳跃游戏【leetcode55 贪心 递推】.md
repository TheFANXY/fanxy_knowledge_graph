# <font color="bb000">跳跃游戏 leetcode 55 贪心</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

## 题目介绍

给定一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标。

**示例 1：**

```
输入：nums = [2,3,1,1,4]
输出：true
解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
```

**示例 2：**

```
输入：nums = [3,2,1,0,4]
输出：false
解释：无论怎样，总会到达下标为 3 的位置。但该下标的最大跳跃长度是 0 ， 所以永远不可能到达最后一个下标。
```

 

**数据范围**

- `1 <= nums.length <= 3 * 104`
- `0 <= nums[i] <= 105`

## 思路

直觉第一写法，直接暴力，`dfs`，但是TLE了，显然这道题对时间限制蛮大，即便加了hash表融合记忆化搜索，还是过不了。

```java
class Solution {
    public boolean canJump(int[] nums) {
        if(nums==null || nums.length==0) {
            return true;
        }
        Map<Integer,Boolean> cache = new HashMap<Integer,Boolean>();
        return dfs(0,nums,cache);
    }
    
    private boolean dfs(int index, int[] nums, Map<Integer,Boolean> cache) {
        if(index>=nums.length-1) {
            return true;
        }
        if(cache.containsKey(index)) {
            return cache.get(index);
        }
        for(int i=1;i<=nums[index];++i) {
            if(dfs(i+index,nums,cache)) {
                cache.put((i+index),Boolean.TRUE);
                return true;
            }
        }
        cache.put(index,Boolean.FALSE);
        return false;
    }
}	
```



后面想到，其实可以使用动态规划，每个点判断它能否扩大可达的范围，遍历每个点，最后判断 `dp[nums.length - 1]`的值。

进一步突然想到，`dp`需要枚举每个点，一步步更新，但是显然，这道题很早的时候就能决定最远可达范围，没必要继续动态规划下去，最后想到，可以利用贪心，只在最远可达点内循环，如果能到达更远的点，就更新最远可达范围，一旦可以跳出循环，就return。

```java
class Solution {
    public boolean canJump(int[] nums) {
        if (nums == null || nums.length == 1) return true;
        int ans = nums[0];
        
        for(int i = 0; i <= ans; i ++) {
            if (ans >= nums.length - 1) return true;
            ans = Math.max(ans, i + nums[i]);
        }
        return false;
    }
}
```









