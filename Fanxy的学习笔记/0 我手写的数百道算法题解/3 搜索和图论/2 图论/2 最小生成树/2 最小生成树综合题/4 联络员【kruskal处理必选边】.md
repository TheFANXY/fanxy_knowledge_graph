# <font color='bb000'>络员-kruskal处理必选边</font>
## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

## 题目介绍

Tyvj已经一岁了，网站也由最初的几个用户增加到了上万个用户，随着Tyvj网站的逐步壮大，管理员的数目也越来越多，现在你身为Tyvj管理层的联络员，希望你找到一些通信渠道，使得管理员两两都可以联络（直接或者是间接都可以）。本题中所涉及的通信渠道都是 双向 的。

Tyvj是一个公益性的网站，没有过多的利润，所以你要尽可能的使费用少才可以。

目前你已经知道，Tyvj的通信渠道分为两大类，一类是必选通信渠道，无论价格多少，你都需要把所有的都选择上；还有一类是选择性的通信渠道，你可以从中挑选一些作为最终管理员联络的通信渠道。

数据保证给出的通信渠道可以让所有的管理员联通。

注意： 对于某两个管理员 u,v，他们之间可能存在多条通信渠道，你的程序应该累加所有 u,v 之间的必选通行渠道。

**输入格式**

第一行两个整数 n，m 表示Tyvj一共有 n 个管理员，有 m 个通信渠道;

第二行到 m+1 行，每行四个非负整数，p,u,v,w 当 p=1 时，表示这个通信渠道为必选通信渠道；当 p=2 时，表示这个通信渠道为选择性通信渠道；u,v,w 表示本条信息描述的是 u，v 管理员之间的通信渠道，u 可以收到 v 的信息，v 也可以收到 u 的信息，w 表示费用。

**输出格式**

一个整数，表示最小的通信费用。

**数据范围**

```java
1 ≤ n ≤ 2000

1 ≤ m ≤ 10000
```

**输入样例：**
```java
5 6
1 1 2 1
1 2 3 1
1 3 4 1
1 4 1 1
2 2 5 10
2 2 5 5
```

**输出样例：**

```java
9
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

这道题一眼最小生成树，同时有了必选边和非必选边，意味着输入过程中，就可以先按必选给生成森林里面加入连通块的并查集关系【`kruskal`】，然后对非必选边排序进行【`kruskal`】即可。

题目保证有解，故生成的是最小生成树而不是森林。

### java 

```java
import java.util.*;
import java.io.*;

public class Main {
    
    static int N = 10010;
    static int n, m, cnt, res;
    static int [] p = new int [N];
    
    static int find (int x){
        if (p[x] != x) p[x] = find(p[x]);
        return p[x];
    }
        
    static class Node implements Comparable<Node>{
        
        int a, b, w;
        
        public Node(int a, int b, int w){
            this.a = a;
            this.b = b;
            this.w = w;
        }
        
        @Override
        public int compareTo(Node o){
            return this.w - o.w;
        }
    }
    
    static Node [] nodes = new Node[N];
        
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    public static void main(String [] args) throws IOException {
        String [] s1 = br.readLine().split(" ");
        n = Integer.parseInt(s1[0]);
        m = Integer.parseInt(s1[1]);
        for (int i = 1; i <= n; i ++) p[i] = i;
        for (int i = 0; i < m; i ++){
            String [] s2 = br.readLine().split(" ");
            int type = Integer.parseInt(s2[0]);
            int a = Integer.parseInt(s2[1]);
            int b = Integer.parseInt(s2[2]);
            int w = Integer.parseInt(s2[3]);
            if (type == 1) {
                res += w;
                p[find(a)] = find(b);
            } else {
                nodes[cnt++] = new Node(a, b, w);
            }
        }
        Arrays.sort(nodes, 0, cnt);
        for (int i = 0; i < cnt; i ++){
            Node node = nodes[i];
            int a = find(node.a), b = find(node.b), w = node.w;
            if (a != b){
                res += w;
                p[a] = b;
            }
        }
        System.out.println(res);
    }
}
```