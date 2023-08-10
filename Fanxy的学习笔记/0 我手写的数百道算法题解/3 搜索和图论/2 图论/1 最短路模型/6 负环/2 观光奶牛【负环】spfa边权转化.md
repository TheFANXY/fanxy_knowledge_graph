# <font color='bb000'>观光奶牛【负环】spfa可以把边权转化</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

## 题目介绍

给定一张 L  个点、P  条边的有向图，每个点都有一个权值 `f[i]` ，每条边都有一个权值 `t[i]` 。

求图中的一个环，使“环上各点的权值之和”除以“环上各边的权值之和”最大。

输出这个最大值。

**注意**：数据保证至少存在一个环。

#### 输入格式

第一行包含两个整数 L  和 P 。

接下来 L  行每行一个整数，表示 `f[i]`。

再接下来 P  行，每行三个整数 a，b，`t[i]` 表示点 a  和 b  之间存在一条边，边的权值为 `t[i]`。

#### 输出格式

输出一个数表示结果，保留两位小数。

#### 数据范围

```java
2 ≤ L ≤ 1000

2 ≤ P ≤ 5000

1 ≤ f[i], t[i] ≤ 1000
```

#### 输入样例：

```java
5 7
30
10
10
5
10
1 2 3
2 3 2
3 4 5
3 5 2
4 5 5
5 1 3
5 2 2
```

#### 输出样例：

```java
6.00
```

----------

一般ACM或者笔试题的时间限制是1秒或2秒。在这种情况下，代码中的操作次数控制在 `10 ^ 7 ∼ 10 ^ 8` 为最佳。

`n <= 100` -> `O(n ^ 3)` -> 状态压缩`dp` `floyd` 高斯消元

`n <= 1000` -> `O(n ^ 2)` `O(n ^ 2 * log(n))` -> `dp`，二分，朴素版Dijkstra、朴素版Prim、Bellman-Ford

`n ≤ 100000`  -> `O(nlogn)` -> 各种sort，线段树、树状数组、set/map、heap、拓扑排序、dijkstra+heap、prim+heap、Kruskal、`spfa`

## [我的SPFA全题解](https://www.acwing.com/solution/content/184825/) 

##  [我的Dijkstra全题解](https://www.acwing.com/solution/content/184816/) 

## [我的Bellman_fold全题解](https://www.acwing.com/solution/content/189425/)

## [我的Floyd全题解](https://www.acwing.com/solution/content/189426/)

##  [我的Prim题解](https://www.acwing.com/solution/content/143780/)

##  [我的Kruskal题解](https://www.acwing.com/solution/content/189531/)


## 解析

这道题是求得是某种条件下得一个最值，一般在一定条件下的最值，或者是第`k`大的值，都可以先看看是否满足某种两边相反的性质，即在一个单调区间左边和右边性质相反。

这道题让找到图中的环，且满足`ΣwPoint  / ΣwEdge`的值最大，这里我们可以想一下，我们二分去寻找一个 `k`值，满足 `ΣwPoint  / ΣwEdge` 大于等于 `k` ，显然当满足图中存在这样的环的情况下，随着 `k` 的增大，满足的环会越来越少，即满足单调性，且左边的 `k`势必不存在答案，因为当前的 `k`已经比左边任何的 `k` 大了。而右边只要满足不等式存在， 这个 `k` 就比当前 `k` 更优。即可以二分。

我们对等式进行变形

```sh
Σ(wPoint[i])  / Σ(wEdge[i]) >= k
Σ(wPoint[i]) - Σ(wEdge[i]) * k >= 0
Σ {wPoint[i] - wEdge[i] * k} >= 0
```

事实上我们可以把点权放到边上，此时当锁定一个环的情况下，点权之和和边权之和不会变化。

上述公式可以看作在一个环中，每个点对应的出边 `wPoint[i] - wEdge[i] * k` 求和后为正，我们融合点权和边权，可以看作相当于把边变形为 `wPoint[i] - wEdge[i] * k` 环中所有的边，均满足为正，即这个环为正环。

倒转`spfa`求负环思路，在最短路中求负环，相当于即在最长路中求正环。

此题证毕。


```java
import java.io.*;
import java.util.*;

public class Main {
    
    static int N = 1010, M = 5010;
    static int n, m, idx;
    static int [] e = new int [M];
    static int [] h = new int [N];
    static int [] ne = new int [M];
    static int [] wEdge = new int [M];
    static double [] d = new double [N];
    static int [] wPoint = new int [N];
    static int [] cnt = new int [N];
    static boolean [] st = new boolean [N];
     
    static void add(int a, int b, int c){
        e[idx] = b;
        ne[idx] = h[a];
        wEdge[idx] = c;
        h[a] = idx++;
    }
    
    static boolean check(double k) {
        Arrays.fill(d, 0);
        Arrays.fill(cnt, 0);
        Arrays.fill(st, false);
        
        LinkedList<Integer> q = new LinkedList<>();
        for (int i=1; i<=n; i++) {
            q.add(i);
            st[i] = true;
        }
        
        while (!q.isEmpty()){
            int top = q.remove();
            st[top] = false;
            
            for (int i=h[top]; i!=-1; i=ne[i]){
                int j = e[i];
                if (d[j] < d[top] + wPoint[top] - wEdge[i] * k) {
                    d[j] = d[top] + wPoint[top] - wEdge[i] * k;
                    cnt[j] = cnt[top] + 1;
                    if (cnt[j] >= n) return true;
                    if (!st[j]) {
                        st[j] = true;
                        q.add(j);
                    }
                }
            }
        }
        
        return false;
    }
    
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    public static void main(String [] args) throws IOException {
        Arrays.fill(h, -1);    
        String [] s1 = br.readLine().split(" ");
        n = Integer.parseInt(s1[0]);
        m = Integer.parseInt(s1[1]);
        for (int i=1; i<=n; i++) wPoint[i] = Integer.parseInt(br.readLine());
        for (int i=1; i<=m; i++){
            String [] s2 = br.readLine().split(" ");
            int a = Integer.parseInt(s2[0]);
            int b = Integer.parseInt(s2[1]);
            int c = Integer.parseInt(s2[2]);
            add(a, b, c);
        }
        double l = 0, r = 1000;
        while (r - l > 1e-4){
            double mid = (l + r) / 2.0;
            if (check(mid)) l = mid;
            else r = mid;
        }
        System.out.printf("%.2f", l);
    }
}
```