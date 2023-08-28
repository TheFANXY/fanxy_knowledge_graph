# <font color="bb000">接雨水 【leetcode42 模拟或栈】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

 

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png)

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 
```

**示例 2：**

```
输入：height = [4,2,0,3,2,5]
输出：9
```

 

**提示：**

- `n == height.length`
- `1 <= n <= 2 * 10^4`
- `0 <= height[i] <= 10^5`



### 解析



#### 模拟法

我们可以逐个分析每一格堆积的雨水，然后累加。

其实不难看出，只取决于高度`【因为宽度固定为1】`，应该是左边最长的柱子和右边最长柱子的最小值，是最大高度，还需要减去柱子本身的高度，但是如果左边右边没有比它更高的柱子，相减就是负数了，即这格不会堆积雨水，为了防止特判，直接把当前格子的高度融入左边最高和右边最高的集合。这样如果左边没有比当前格子高的集合，相减就是 `0` 了。

即做法就是通过两次 `O(n)` 的遍历，把每个格子左边最高和右边最高记录。然后最后用一次 `O(n)` 的遍历把每个格子的雨水累加，整个复杂度，从时间和空间上都是 `O(n)`

```java
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        if (n <= 1) return 0;
        int [] left_max = new int [n];
        int [] right_max = new int [n];
        left_max[0] = height[0];
        for (int i = 1; i < n; i ++) left_max[i] = Math.max(height[i], left_max[i-1]);
        right_max[n-1] = height[n-1];
        for (int i = n - 2; i >= 0; i --) right_max[i] = Math.max(height[i], right_max[i + 1]);

        int ans = 0;
        for (int i = 0; i < n; i ++)
            ans += Math.min(left_max[i], right_max[i]) - height[i];
        
        return ans;
    }
}
```



#### 栈

这里相当于上面的考虑，换成了每次考虑横着的格子，仍然是出现新的格子，如果比前面的格子高，且这个中间的格子前面还有比它高的格子，才可能有水。

那么我们可以枚举每个格子，都入栈，如果栈顶的格子和它高度相等或者更矮，就把它弹出【相等移除是因为同样高度的连续长度考虑一次就够了，没必要考虑多次，宽度可以直接使用下标相减获得，不需要靠枚举数量】，如果弹出之后，栈顶还有元素，那就可以形成一个凹槽。

其水高度为两边的最短边，宽度为 `i - stk.peek() - 1` ，即排除两端点的中间节点数量。

将这段面积加入答案即可。

```java
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        LinkedList<Integer> stk = new LinkedList<>();
        int ans = 0;

        for (int i = 0; i < n; i ++) {
            while(!stk.isEmpty() && height[i] >= height[stk.peek()]) {
                int mid = stk.pop();
                
                if (!stk.isEmpty()) {
                    int h = Math.min(height[i], height[stk.peek()]) - height[mid];
                    ans += h * (i - stk.peek() - 1);
                }
            }
            stk.push(i);
        }
        return ans;
    }
}
```







