# <font color="bb000">最优贸易——【解析下为什么必须反向建图】</font>
## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

## 解析

## 这道题只能使用`SPFA`，因为是存在负权边的

一般ACM或者笔试题的时间限制是1秒或2秒。在这种情况下，代码中的操作次数控制在 `10 ^ 7 ∼ 10 ^ 8` 为最佳。

`n <= 100` -> `O(n ^ 3)` -> 状态压缩dp floyd 高斯消元

`n <= 1000` -> `O(n ^ 2)` `O(n ^ 2 * log(n))` -> dp，二分，朴素版Dijkstra、朴素版Prim、Bellman-Ford

`n ≤ 100000`  -> `O(nlogn)` -> 各种sort，线段树、树状数组、set/map、heap、拓扑排序、dijkstra+heap、prim+heap、Kruskal、spfa

## [我的SPFA全题解](https://www.acwing.com/solution/content/184825/) 

##  [我的Dijkstra全题解](https://www.acwing.com/solution/content/184816/) 

## [我的Bellman_fold全题解](https://www.acwing.com/solution/content/189425/)

## [我的Floyd全题解](https://www.acwing.com/solution/content/189426/)


## 解析

## 为什么不能使用Dijkstra

能使用Dijkstra的原则是，当我们从小根堆拿出距离最小的点，更新其他的邻接点的边，是可以保证它未来不会被其他点更新的更小的。固非负权图可以满足，因为但凡后面的点还能到此点，只能让距离不变或更大。

那这道题，题目没有保证没有环，那么找寻最小的情况，可能第一次到达点 `i`，此时它前面的点更新到它，最小的确定出来了是`dmin[i]`，使用 `dijkstra`更新完后如果真的把它锁定，日后仍可能遇到更小的点，且能走回`dmin[i]`，从某种角度讲，这道题并不是看边权，而是看点权，故只要走到一个相对比之前`dmin[i]`小的点，且还能走回`i`，就相当于有了负权边，故此时`dijkstra` 失效。

## 由图结构到DP

我们这道题首先根据题意判断，价值首先不在边权，而是点权。其次本题说所有点可以经过 `无限` 次，故并不是一次最短路解决问题。说明存在非最短路最优的情况，要么需要改造最短路算法，要么需要组合一些别的算法。

因为阿龙主要是来 `C` 国旅游，他决定这个贸易只进行最多一次，当然，在赚不到差价的情况下他就无需进行贸易。故直觉思路就是找寻所有到 `N` 的路径，然后把路径中最大的点权减去最小的点权。

### 那么该如何把所有的路径遍历？

当然，dfs+记忆化肯定是能遍历所有情况的，bfs+记忆节点状态应该也可以，但是复杂度可能有些大【其实有人提供了这样的题解，大家可以去看看】

其实可以使用 `DP` 的思想，我们可以找寻到达点 `i`的最小点权【包括`i`】，然后找寻从点`i`到达点`n`的最大点权。

然后进行求差值。

## 那为什么反向建图？只用正向图难道不能算`i`到`n`了不成？

有这个疑问的，需要好好思考一下，最短路算的距离，最后得到的`d[n]`，是`1 -> n`的最短路，我们初始化距离为无穷，然后初始化起点为 `0`，所以最短路算法算的是固定起点，算到所有终点的距离。

故我们如果想用正向图算`i -> n`，那岂不是每枚举到一个点 `i`，都需要算一次正向最短路，才能算出 `i -> n`。

### 但如果反向建图求最短路

我们反向建图，可以求得`n -> 任何点`的最短路，但我们知道，实际的边是正向的，故反向图的`n -> 任何点`的最短路，等价于实际的正向图`任何点 -> n`的最短路。

故我们使用了反向图，就把一个`O(n ^ 2)`问题，变成了`O(2n)`的问题。这里指的是两个建图方式的最短路复杂度对比，而不是实际的复杂度。

实际情况下，瓶颈是SPFA，SPFA算法的时间复杂度是O(km)，其中k一般情况下是个很小的常数，最坏情况下是n,n表示总点数，m表示总边数。因此总时间复杂度是 O(km)，至于两次`spfa`也算是常数，就归入 `k` 中了。


## java

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.LinkedList;


/**
 * @author Fanxy
 */
public class Main {

    private static final int INF = 0x3f3f3f3f;
    static int n, m, idx;
    static int N = 100010, M = 2000010;
    static int[] hDirect = new int[N];
    static int[] hReverse = new int[N];
    static int[] e = new int[M];
    static int[] ne = new int[M];
    static int[] dMin = new int[N];
    static int[] dMax = new int[N];
    static int[] w = new int[N];
    static boolean[] st = new boolean[N];

    static void add(int[] h, int a, int b) {
        e[idx] = b;
        ne[idx] = h[a];
        h[a] = idx++;
    }

    // type 1 : 代表正向图求最小值
    // type 2 : 代表反向图求最大值
    static void spfa(int type, int[] h, int [] d) {
        LinkedList<Integer> queue = new LinkedList<>();
        if (type == 1) {
            Arrays.fill(d, INF);
            d[1] = w[1];
            queue.add(1);
        } else {
            Arrays.fill(d, -INF);
            d[n] = w[n];
            queue.add(n);
        }

        while (!queue.isEmpty()) {
            int root = queue.remove();
            st[root] = false;
            for (int i = h[root]; i != -1; i = ne[i]) {
                int son = e[i];
                if ((type == 1 && d[son] > Math.min(d[root], w[son])) || (type == 2 && d[son] < Math.max(d[root], w[son]))) {
                    if (type == 1) d[son] = Math.min(d[root], w[son]);
                    else d[son] = Math.max(d[root], w[son]);
                    if (!st[son]){
                        queue.add(son);
                        st[son] = true;
                    }
                }
            }
        }
    }

    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    public static void main(String[] args) throws IOException {
        Arrays.fill(hDirect, -1);
        Arrays.fill(hReverse, -1);
        String[] s1 = br.readLine().split(" ");
        n = Integer.parseInt(s1[0]);
        m = Integer.parseInt(s1[1]);
        String[] s2 = br.readLine().split(" ");
        for (int i = 1; i <= n; i++) w[i] = Integer.parseInt(s2[i-1]);
        for (int i = 0; i < m; i++) {
            String[] s3 = br.readLine().split(" ");
            int a = Integer.parseInt(s3[0]);
            int b = Integer.parseInt(s3[1]);
            int type = Integer.parseInt(s3[2]);
            add(hDirect, a, b);
            add(hReverse, b, a);
            if (type == 2) {
                add(hDirect, b, a);
                add(hReverse, a, b);
            }
        }
        // 正向图反向图均进行spfa
        spfa(1, hDirect, dMin);
        spfa(2, hReverse, dMax);
        
        // 枚举答案
        int res = -INF;
        for (int i = 1; i <= n; i++) res = Math.max(res, dMax[i] - dMin[i]);
        System.out.println(res);
    }
}
```