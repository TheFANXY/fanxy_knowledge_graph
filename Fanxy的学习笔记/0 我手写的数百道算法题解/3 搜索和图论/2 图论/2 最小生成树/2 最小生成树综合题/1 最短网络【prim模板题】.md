# <font color='bb000'>最短网络-最小生成树模板题</font>
## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

## 题目介绍

农夫约翰被选为他们镇的镇长！

他其中一个竞选承诺就是在镇上建立起互联网，并连接到所有的农场。

约翰已经给他的农场安排了一条高速的网络线路，他想把这条线路共享给其他农场。

约翰的农场的编号是1，其他农场的编号是 2∼n。

为了使花费最少，他希望用于连接所有的农场的光纤总长度尽可能短。

你将得到一份各农场之间连接距离的列表，你必须找出能连接所有农场并使所用光纤最短的方案。

**输入格式**

第一行包含一个整数 n，表示农场个数。

接下来 n 行，每行包含 n 个整数，输入一个对角线上全是0的对称矩阵。
其中第 x+1 行 y 列的整数表示连接农场 x 和农场 y 所需要的光纤长度。

**输出格式**

输出一个整数，表示所需的最小光纤长度。

**数据范围**

```java
3 ≤ n ≤ 100
 
每两个农场间的距离均是非负整数且不超过100000。
```

**输入样例：**
```java
4
0  4  9  21
4  0  8  17
9  8  0  16
21 17 16  0
```

**输出样例：**

```java
28
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

一眼即模板题，且给了邻接矩阵，显然使用prim更友好，kruskal复杂度低，但是要构造结构体数组，给邻接矩阵转化比较麻烦。

剩下的套模板，且题目保证有解，不需要判断是否无解的情况。


```java
import java.util.Arrays;
import java.util.Scanner;

/**
 * @author Fanxy
 */
public class Main {
    static final int INF = 0x3f3f3f3f;
    static int N = 110;
    static int n, m, ans;
    static int[][] g = new int[N][N];
    static int[] d = new int[N];
    static boolean[] st = new boolean[N];

    static void prim() {
        Arrays.fill(d, INF);
        d[1] = 0;
        for (int i = 0; i < n; i++) {
            int t = -1;
            for (int j = 1; j <= n; j++)
                if (!st[j] && (t == -1 || d[j] < d[t]))
                    t = j;

            st[t] = true;
            ans += d[t];
            for (int j = 1; j <= n; j++) d[j] = Math.min(d[j], g[t][j]);
        }
        System.out.println(ans);
    }

    static Scanner sc = new Scanner(System.in);

    public static void main(String[] args) {
        n = sc.nextInt();
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                g[i][j] = sc.nextInt();
            }
        }
        prim();
    }
}
```