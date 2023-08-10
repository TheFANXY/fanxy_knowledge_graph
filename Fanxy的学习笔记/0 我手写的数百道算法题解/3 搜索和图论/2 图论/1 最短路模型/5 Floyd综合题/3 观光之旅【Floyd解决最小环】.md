# <font color="bb000">观光之旅—Floyd解决最小环</font>
## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

## 题目介绍

给定一张无向图，求图中一个至少包含 3 个点的环，环上的节点不重复，并且环上的边的长度之和最小。

该问题称为无向图的最小环问题。

你需要输出最小环的方案，若最小环不唯一，输出任意一个均可。

**输入格式**

第一行包含两个整数 N 和 M，表示无向图有 N 个点，M 条边。

接下来 M 行，每行包含三个整数 u，v，l，表示点 u 和点 v 之间有一条边，边长为 l。

**输出格式**

输出占一行，包含最小环的所有节点（按顺序输出），如果不存在则输出 `No solution.`。

**数据范围**

```java
1 ≤ N ≤ 100
 
1 ≤ M ≤ 10000
  
1 ≤ l < 500
```

**输入样例：**
```java
5 7
1 4 1
1 3 300
3 1 10
1 2 16
2 3 100
2 5 15
5 3 20
```

**输出样例：**

```java
1 3 5 2
```

----------

一般ACM或者笔试题的时间限制是1秒或2秒。在这种情况下，代码中的操作次数控制在 `10 ^ 7 ∼ 10 ^ 8` 为最佳。

`n <= 100` -> `O(n ^ 3)` -> 状态压缩dp floyd 高斯消元

`n <= 1000` -> `O(n ^ 2)` `O(n ^ 2 * log(n))` -> dp，二分，朴素版Dijkstra、朴素版Prim、Bellman-Ford

`n ≤ 100000`  -> `O(nlogn)` -> 各种sort，线段树、树状数组、set/map、heap、拓扑排序、dijkstra+heap、prim+heap、Kruskal、spfa

## [我的SPFA全题解](https://www.acwing.com/solution/content/184825/) 

##  [我的Dijkstra全题解](https://www.acwing.com/solution/content/184816/) 

## [我的Bellman_fold全题解](https://www.acwing.com/solution/content/189425/)

## [我的Floyd全题解](https://www.acwing.com/solution/content/189426/)


## 解析

这题需要深入挖掘`floyd`的底层原理，才能清楚的明白为什么可以利用floyd的性质来挖掘传递闭包。


## floyd的原理

本质 `floyd` 就是属于利用动态规划来进行解决多源最短路问题，但是不能有负环【会产生负无穷的距离】

这里其实优化掉了一维，即 `k` ，这个参数指的是从前 `1 -> k` 的点选取中间点，枚举插入`i -> j`中，是否能使得距离更小并更更新。

而发现`g[k][i][j] = Math.min(g[k-1][i][j], g[k-1][i][k] + g[k-1][k][j])` 只依赖于更小一维的方程，而由于递增枚举，不会污染答案，故直接优化掉这一维度的空间。

```java
for (int k = 1; k <= n; i++)
    for (int i = 1; i <= n; i++) 
        for (int j = 1; j <= n; j++)
            g[i][j] = Math.min(g[i][j], g[i][k] + g[k][j])
```

## 本题进一步延申

首先我们为了记录路径，由 `floyd` 的性质，我们枚举的插入点是递增的 `1 -> n`，由最开始生成的图作为基础图关系，在此基础上进行 `floyd`，相对于原图已存在的边，如果能进行更新，说明找到一条更短的边，此时这两个边就构成了环。

那么为了找寻最小的环，只需要用一个全局答案，去保存最小值即可。

## 那么路径该如何确定？

首先我们可以确定，枚举更新的中间点是递增的，也就是说最终一定确定的最短路经过的`k`是`k`可取的最大的序号端点。

且当确定最短路的情况下，一定路径上没有环【显然，如果有环，显然我们不经过环直接走到终点更近，违背了最短路的前提】

## 其次`i -> k` 和 `k -> j`的路径势必没有交集

显然，如果存在交集的情况，势必我们可以从交集的点中，找寻一条不走重复边的路线，这条路线比走重复边的情况更短，同样违背最短路的前提

因此，我们可以利用一个数组`pos[i][j]`，唯一保存`i -> j`最短路所经过的端点 `k`，利用递归和分治的思想，且有上面证明的没有交集的前提，可以找寻到`i -> j`最短路经过的所有中间点，这条路径加上最开始的原图路径，即是这两点的最小环，不断枚举`i`和`j`的过程去更新全局答案，即找寻到了全局的最小环。


----------

## java

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;


/**
 * @author Fanxy
 */
public class Main {
    static final int INF = 0x3f3f3f3f;
    static int N = 110;
    static int n, m;
    static int[][] g = new int[N][N];
    static int[][] d = new int[N][N];
    static int[][] pos = new int[N][N];
    static ArrayList<Integer> path = new ArrayList<Integer>();

    // 精妙的递归 保存沿途的中间点
    // 底层依赖了 如果 i -> j 没有经过点 k 势必 pos[i][j] == 0
    static void dfs(int i, int j) {
        int k = pos[i][j];
        if (k == 0) return;
        dfs(i, k);
        path.add(k);
        dfs(k, j);
    }

    static void getPath(int i, int j, int k) {
        path.clear();
        path.add(k);
        path.add(i);
        dfs(i, j);
        path.add(j);
    }

    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    public static void main(String[] args) throws IOException {
        String[] s1 = br.readLine().split(" ");
        n = Integer.parseInt(s1[0]);
        m = Integer.parseInt(s1[1]);
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                if (i != j) g[i][j] = INF;
            }
        }
        for (int i = 0; i < m; i++) {
            String[] s2 = br.readLine().split(" ");
            int a = Integer.parseInt(s2[0]);
            int b = Integer.parseInt(s2[1]);
            int c = Integer.parseInt(s2[2]);
            g[a][b] = g[b][a] = Math.min(g[a][b], c);
        }
        for (int i = 1; i <= n; i++) {
            d[i] = g[i].clone();
        }
        long ans = INF;
        for (int k = 1; k <= n; k++) {
            // 类似组合型枚举 我们定义一个严格的顺序 把重复的情况排除
            for (int i = 1; i < k; i++) {
                for (int j = i + 1; j < k; j++) {
                    if (ans > (long) g[i][j] + d[i][k] + d[k][j]){
                        ans = g[i][j] + d[i][k] + d[k][j];
                        getPath(i, j, k);
                    }
                }
            }

            for (int i = 1; i <= n; i++) {
                for (int j = 1; j <= n; j++) {
                    if (g[i][j] > g[i][k] + g[k][j]) {
                        g[i][j] = g[i][k] + g[k][j];
                        pos[i][j] = k;
                    }
                }
            }
        }
        if (ans == INF) System.out.println("No solution.");
        else {
            for (Integer x : path) System.out.printf(x + " ");
        }
    }
}
```