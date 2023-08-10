# <font color="bb000">选择最佳线路—多源BFS和反向图</font>
## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

## 题目介绍

有一天，琪琪想乘坐公交车去拜访她的一位朋友。

由于琪琪非常容易晕车，所以她想尽快到达朋友家。

现在给定你一张城市交通路线图，上面包含城市的公交站台以及公交线路的具体分布。

已知城市中共包含 n 个车站（编号1 ~ n）以及 m 条公交线路。

每条公交线路都是 单向的，从一个车站出发直接到达另一个车站，两个车站之间可能存在多条公交线路。

琪琪的朋友住在 s 号车站附近。

琪琪可以在任何车站选择换乘其它公共汽车。

请找出琪琪到达她的朋友家（附近的公交车站）需要花费的最少时间。

**输入格式**

输入包含多组测试数据。

每组测试数据第一行包含三个整数 n,m,s 分别表示车站数量，公交线路数量以及朋友家附近车站的编号。

接下来 m 行，每行包含三个整数 p,q,t，表示存在一条线路从车站 p 到达车站 q，用时为 t。

接下来一行，包含一个整数 w，表示琪琪家附近共有 w 个车站，她可以在这 w 个车站中选择一个车站作为始发站。

再一行，包含 w 个整数，表示琪琪家附近的 w 个车站的编号。

**输出格式**

每个测试数据输出一个整数作为结果，表示所需花费的最少时间。

如果无法达到朋友家的车站，则输出 -1。

每个结果占一行。

**数据范围**
```sh
n ≤ 1000, m ≤ 20000
 
1 ≤ s ≤ n
 
0 < w < n
 
0 < t ≤ 1000
```

**输入样例：**
```sh
5 8 5
1 2 2
1 5 3
1 3 4
2 4 7
2 5 6
2 3 5
3 5 1
4 5 1
2
2 3
4 3 4
1 2 3
1 3 4
2 3 2
1
1
```

**输出样例：**
```sh
1
-1
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

##  [我的多源BFS题解【矩阵距离】](https://www.acwing.com/solution/content/193067/) 

多源bfs本质上就是的创建虚拟源点，这个源点到多源的起点均为0的距离，那么只用算单源最短路，就能解决问题。

代码实现也很简单，因为图论问题一般都用不到0这个下标的点，就把它当成源点建立到每个起点的0权边即可。

注意我们额外添加了边，故数据大小得多拓展起点的最大数量即1000个长度。

同时这道题也可以利用反向图，算终点到其他点的单源最短路，具体细节不过多赘述，可以看我之前的最优贸易题解。

##  [最优贸易题解`dp+反向图解决复杂度`](https://www.acwing.com/solution/content/197315/)

----------

## java多源最短路

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
    static int n, m, end, idx;
    static int N = 1010, M = 21010;
    static int[] e = new int[M];
    static int[] ne = new int[M];
    static int[] w = new int[M];
    static int[] h = new int[N];
    static int[] d = new int[N];
    static boolean[] st = new boolean[N];

    static void add(int a, int b, int c) {
        e[idx] = b;
        ne[idx] = h[a];
        w[idx] = c;
        h[a] = idx++;
    }

    static int spfa() {
        Arrays.fill(d, INF);
        d[0] = 0;
        LinkedList<Integer> queue = new LinkedList<>();
        queue.add(0);
        while (!queue.isEmpty()) {
            int top = queue.remove();
            st[top] = false;
            for (int i = h[top]; i != -1; i = ne[i]) {
                int j = e[i];
                if (d[j] > d[top] + w[i]) {
                    d[j] = d[top] + w[i];
                    if (!st[j]){
                        queue.add(j);
                        st[j] = true;
                    }
                }
            }
        }
        if (d[end] == INF) d[end] = -1;
        return d[end];
    }

    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    public static void main(String[] args) throws IOException {
        while (true) {
            Arrays.fill(h, -1);
            idx = 0;
            String s = br.readLine();
            if (s == null) break;
            
            String[] s1 = s.split(" ");
            n = Integer.parseInt(s1[0]);
            m = Integer.parseInt(s1[1]);
            end = Integer.parseInt(s1[2]);
            for (int i = 0; i < m; i++) {
                String[] s2 = br.readLine().split(" ");
                int a = Integer.parseInt(s2[0]);
                int b = Integer.parseInt(s2[1]);
                int c = Integer.parseInt(s2[2]);
                add(a, b, c);
            }
            int count = Integer.parseInt(br.readLine());
            String[] s3 = br.readLine().split(" ");
            for (int i = 0; i < count; i++) {
                int t = Integer.parseInt(s3[i]);
                add(0, t, 0);
            }
            System.out.println(spfa());
        }
    }
}
```

## java反向图 
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
    static int n, m, end, idx;
    static int N = 1010, M = 20010;
    static int[] e = new int[M];
    static int[] ne = new int[M];
    static int[] w = new int[M];
    static int[] h = new int[N];
    static int[] d = new int[N];
    static boolean[] st = new boolean[N];

    static void add(int a, int b, int c) {
        e[idx] = b;
        ne[idx] = h[a];
        w[idx] = c;
        h[a] = idx++;
    }

    static void spfa() {
        Arrays.fill(d, INF);
        d[end] = 0;
        LinkedList<Integer> queue = new LinkedList<>();
        queue.add(end);
        while (!queue.isEmpty()) {
            int top = queue.remove();
            st[top] = false;
            for (int i = h[top]; i != -1; i = ne[i]) {
                int j = e[i];
                if (d[j] > d[top] + w[i]) {
                    d[j] = d[top] + w[i];
                    if (!st[j]){
                        queue.add(j);
                        st[j] = true;
                    }
                }
            }
        }
    }

    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    public static void main(String[] args) throws IOException {
        while (true) {
            Arrays.fill(h, -1);
            String s = br.readLine();
            if (s == null) break;
            idx = 0;
            String[] s1 = s.split(" ");
            n = Integer.parseInt(s1[0]);
            m = Integer.parseInt(s1[1]);
            end = Integer.parseInt(s1[2]);
            for (int i = 0; i < m; i++) {
                String[] s2 = br.readLine().split(" ");
                int a = Integer.parseInt(s2[0]);
                int b = Integer.parseInt(s2[1]);
                int c = Integer.parseInt(s2[2]);
                add(b, a, c);
            }
            spfa();
            int count = Integer.parseInt(br.readLine());
            String[] s3 = br.readLine().split(" ");
            int res = INF;
            for (int i = 0; i < count; i++) {
                int t = Integer.parseInt(s3[i]);
                res = Math.min(res, d[t]);
            }
            if(res == INF) res = -1;
            System.out.println(res);
        }
    }
}
```