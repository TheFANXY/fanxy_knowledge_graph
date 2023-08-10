# <font color='bb000'>局域网-多树的kruskal</font>
## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

## 题目介绍

某个局域网内有 `n` 台计算机和 `k` 条双向网线，计算机的编号是 `1∼n`。由于搭建局域网时工作人员的疏忽，现在局域网内的连接形成了回路，我们知道如果局域网形成回路那么数据将不停的在回路内传输，造成网络卡的现象。

注意：

对于某一个连接，虽然它是双向的，但我们不将其当做回路。本题中所描述的回路至少要包含两条不同的连接。

两台计算机之间最多只会存在一条连接。

不存在一条连接，它所连接的两端是同一台计算机。

因为连接计算机的网线本身不同，所以有一些连线不是很畅通，我们用 `f(i,j)` 表示 `i,j` 之间连接的畅通程度，`f(i,j)` 值越小表示 `i,j` 之间连接越通畅。

现在我们需要解决回路问题，我们将除去一些连线，使得网络中没有回路且不影响连通性（即如果之前某两个点是连通的，去完之后也必须是连通的），并且被除去网线的 `Σf(i,j)` 最大，请求出这个最大值。

**输入格式**

一个正整数，表示被除去网线的 `Σf(i,j)` 的最大值。

**输出格式**

输出一个整数，表示所需的最小光纤长度。

**数据范围**

```java
1 ≤ n ≤ 100

0 ≤ k ≤ 200

1 ≤ f(i,j) ≤ 1000
```

**输入样例：**
```java
5 5
1 2 8
1 3 1
1 5 3
2 4 5
3 4 2
```

**输出样例：**

```java
8
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

##  [我的Prim题解](https://www.acwing.com/solution/content/143780/)

##  [我的Kruskal题解](https://www.acwing.com/solution/content/189531/)


## 解析

这道题一眼最小生成树，因为求去除的边的最大值，即求全局边减去最小生成树的边和。

【而且这道题没有申明全部的点在一棵树，但是`kruskal`使用并查集，可以生成最小生成树森林，可以直接应用于多树的情况，不像`prim`，模板是符合单树情况，而如果算多树，要么使用多次`prim`，要么要对模板进行改造】

求答案可以在输入每个边权的情况下，计算全局边权，然后减去生成森林的边权。

或者是`kruskal`判断两点已经在一个树的情况下，说明更优的树边已经找到了，当前边是应该被删去的边，累加即为答案。

### java 直接累加删除边

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;


/**
 * @author Fanxy
 */
public class Main {
    static final int INF = 0x3f3f3f3f;
    static int N = 110, M = 210;
    static int n, m, ans;

    static class Node implements Comparable<Node> {
        int a, b, w;

        public Node(int a, int b, int w) {
            this.a = a;
            this.b = b;
            this.w = w;
        }

        @Override
        public int compareTo(Node o) {
            return this.w - o.w;
        }
    }

    static int[] p = new int[N];
    static Node[] nodes = new Node[M];
    static int find(int x){
        if (p[x] != x) p[x] = find(p[x]);
        return p[x];
    }
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    public static void main(String[] args) throws IOException {
        String[] s1 = br.readLine().split(" ");
        n = Integer.parseInt(s1[0]);
        m = Integer.parseInt(s1[1]);
        
        for (int i = 0; i < m; i++) {
            String[] s2 = br.readLine().split(" ");
            int a = Integer.parseInt(s2[0]);
            int b = Integer.parseInt(s2[1]);
            int w = Integer.parseInt(s2[2]);
            nodes[i] = new Node(a, b, w);
            if (i <= n) p[i] = i;
        }
        Arrays.sort(nodes, 0, m);

        for (int i = 0; i < m; i++) {
            Node node = nodes[i];
            int a = find(node.a), b = find(node.b), w = node.w;
            if (a != b) p[a] = b;
            else ans += w;
        }
        System.out.println(ans);
    }
}
```

### 全局边减去生成树边

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;


/**
 * @author Fanxy
 */
public class Main {
    static final int INF = 0x3f3f3f3f;
    static int N = 110, M = 210;
    static int n, m, ans;

    static class Node implements Comparable<Node> {
        int a, b, w;

        public Node(int a, int b, int w) {
            this.a = a;
            this.b = b;
            this.w = w;
        }

        @Override
        public int compareTo(Node o) {
            return this.w - o.w;
        }
    }

    static int[] p = new int[N];
    
    static Node[] nodes = new Node[M];
    
    static int find(int x){
        if (p[x] != x) p[x] = find(p[x]);
        return p[x];
    }
    
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    public static void main(String[] args) throws IOException {
        String[] s1 = br.readLine().split(" ");
        n = Integer.parseInt(s1[0]);
        m = Integer.parseInt(s1[1]);
        int total = 0;
        
        for (int i = 0; i < m; i++) {
            String[] s2 = br.readLine().split(" ");
            int a = Integer.parseInt(s2[0]);
            int b = Integer.parseInt(s2[1]);
            int w = Integer.parseInt(s2[2]);
            total += w;
            nodes[i] = new Node(a, b, w);
            if (i <= n) p[i] = i;
        }
        Arrays.sort(nodes, 0, m);

        for (int i = 0; i < m; i++) {
            Node node = nodes[i];
            int a = find(node.a), b = find(node.b), w = node.w;
            if (a != b) {
                ans += w;
                p[a] = b;
            }
        }
        System.out.println(total - ans);
    }
}
```