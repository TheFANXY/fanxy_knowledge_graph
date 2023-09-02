# <font color="bb000">N皇后1【leetcode51 dfs经典题型】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

按照国际象棋的规则，皇后可以攻击与之处在同一行或同一列或同一斜线上的棋子。

**n 皇后问题** 研究的是如何将 `n` 个皇后放置在 `n×n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回所有不同的 **n 皇后问题** 的解决方案。

每一种解法包含一个不同的 **n 皇后问题** 的棋子放置方案，该方案中 `'Q'` 和 `'.'` 分别代表了皇后和空位。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/11/13/queens.jpg)

```java
输入：n = 4
输出：[[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
解释：如上图所示，4 皇后问题存在两个不同的解法。
```

**示例 2：**

```java
输入：n = 1
输出：[["Q"]]
```

 

**提示：**

- `1 <= n <= 9`



### 解析

这道题应该是大多数人`dfs` 入门的第二题，第一题一般是数字的全排列问题，这道题其实最秒的是，没有单独开一个数组存行状态，而是靠 `index` 是否达到了横坐标的极限 `n` 。

而存主对角线和副对角线，主对角线，显然满足 **横坐标减去纵坐标的值相同**，副对角线，显然满足 **横坐标和纵坐标之和的值相同。**

因为枚举所有坐标，万一枚举到横坐标值小于纵坐标的情况，还相减，就会出现数组越界，所以可以加一个 `n` 的常量。

这样保证即便是极限情况 `0 - (n - 1)` ，`1 - n` ，加上 `n` 也是正数。

```java
class Solution {

    static int N = 12;
    static List<List<String>> ans = new ArrayList<>();
    static char [][] g = new char[N][N];
    static boolean [] col = new boolean[N];
    static boolean [] dg = new boolean[N * 2];
    static boolean [] udg = new boolean[N * 2];

    public void dfs(int index, int n) {
        if (index == n) {
            List <String> list = new ArrayList<>();
            for (int i = 0; i < n; i ++) {
                StringBuilder temp = new StringBuilder("");
                for (int j = 0; j < n; j ++) temp.append(g[i][j]);
                list.add(temp.toString());
            }
            ans.add(new ArrayList(list));
            return;
        }

        for (int i = 0; i < n; i ++) {
            if (!col[i] && !dg[index + i] && !udg[n + index - i]) {
                col[i] = dg[index + i] = udg[n + index - i] = true;
                g[index][i] = 'Q';
                dfs(index + 1, n);
                col[i] = dg[index + i] = udg[n + index - i] = false;
                g[index][i] = '.';
            }   
        }
    }

    public List<List<String>> solveNQueens(int n) {
        ans.clear();
        for (int i = 0; i < n; i ++) Arrays.fill(g[i], '.');
        dfs(0, n);
        return ans;
    }
}
```

