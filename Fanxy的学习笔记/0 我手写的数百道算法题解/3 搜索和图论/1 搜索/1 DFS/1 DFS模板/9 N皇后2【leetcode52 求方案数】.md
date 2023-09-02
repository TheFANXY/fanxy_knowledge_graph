# <font color="bb000">N皇后2【leetcode52 求方案数】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

**n 皇后问题** 研究的是如何将 `n` 个皇后放置在 `n × n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回 **n 皇后问题** 不同的解决方案的数量。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/11/13/queens.jpg)

```
输入：n = 4
输出：2
解释：如上图所示，4 皇后问题存在两个不同的解法。
```

**示例 2：**

```
输入：n = 1
输出：1
```

 

**提示：**

- `1 <= n <= 9`



### 解析

上一题已经写了思路，这题求方案，都不需要存数组和改符号，只需要 `boolean 数组` 。

```java
class Solution {

    static int res = 0;
    static int N = 11;
    static boolean [] col = new boolean[N];
    static boolean [] dg = new boolean[2 * N];
    static boolean [] udg = new boolean[2 * N];

    public void dfs(int index, int n) {
        if (index == n) {
            res ++;
            return;
        }

        for (int i = 0; i < n; i ++) {
            if (!col[i] && !dg[index + i] && !udg[index - i + n]) {
                col[i] = dg[index + i] = udg[index - i + n] = true;
                dfs(index + 1, n);
                col[i] = dg[index + i] = udg[index - i + n] = false;
            }
        }
    }
    
    public int totalNQueens(int n) {
        res = 0;
        dfs(0, n);
        return res;
    }
}
```

