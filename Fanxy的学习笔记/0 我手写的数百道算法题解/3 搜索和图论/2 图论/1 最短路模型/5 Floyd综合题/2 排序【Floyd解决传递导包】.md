# <font color="bb000">排序—`floyd`解决传递导包</font>
## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

## 题目介绍

给定 n 个变量和 m 个不等式。其中 n 小于等于 26，变量分别用前 n 的大写英文字母表示。

不等式之间具有传递性，即若 A>B 且 B>C，则 A>C。

请从前往后遍历每对关系，每次遍历时判断：

- 如果能够确定全部关系且无矛盾，则结束循环，输出确定的次序；
- 如果发生矛盾，则结束循环，输出有矛盾；
- 如果循环结束时没有发生上述两种情况，则输出无定解。

**输入格式**

输入包含多组测试数据。

每组测试数据，第一行包含两个整数 n 和 m。

接下来 m 行，每行包含一个不等式，不等式全部为小于关系。

当输入一行 0 0 时，表示输入终止。

**输出格式**

每组数据输出一个占一行的结果。

结果可能为下列三种之一：

如果可以确定两两之间的关系，则输出 `"Sorted sequence determined after t relations: yyy...y."`,其中`'t'`指迭代次数，`'yyy...y'`是指升序排列的所有变量。
如果有矛盾，则输出： `"Inconsistency found after t relations."`，其中`'t'`指迭代次数。
如果没有矛盾，且不能确定两两之间的关系，则输出 `"Sorted sequence cannot be determined."`

**数据范围**

```
2 ≤ n ≤ 26，变量只可能为大写字母 A∼Z。
```

**输入样例：**
```java
4 6
A<B
A<C
B<C
C<D
B<D
A<B
3 2
A<B
B<A
26 1
A<Z
0 0
```

**输出样例：**

```java
Sorted sequence determined after 4 relations: ABCD.
Inconsistency found after 2 relations.
Sorted sequence cannot be determined.
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

这题思路其实不难，但是代码实现比较麻烦，涉及到多个基于的模块之上来打理。

## 本质思想

本质我们使用一个数组 `g[][]` 存储每次大循环的最模板的距离状态数组，而每加入一个新的节点，都会重新基于目前的距离状态数组，再进行一次`floyd`，这种情况下，显然我们每次基于的是 `g[][] 的clone`

为了找寻当前关系的类型，因此额外开`type变量`，用于同时为了记载每次大循环 `type` 变量变化的轮次，额外开了数组给它监控。

具体实现过程见代码

----------

## java

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;


/**
 * @author Fanxy
 */
public class Main {
    static int N = 30;
    static int n, m;
    static boolean[][] g = new boolean[N][N];
    static boolean[][] d = new boolean[N][N];
    static boolean[] st = new boolean[N];

    static void floyd() {
        d = g.clone();
        for (int k = 0; k < n; k++) {
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    d[i][j] |= (d[i][k] && d[k][j]);
                }
            }
        }
    }

    /**
     * 判断所有不等式的关系
     * type = 0 -> 无法确定关系
     * type = 1 -> 有唯一解
     * type = 2 -> 有矛盾
     */
    static int check() {
        for (int i = 0; i < n; i++) if (d[i][i]) return 2;

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (!d[i][j] && !d[j][i])
                    return 0;
            }
        }
        return 1;
    }

    static char getMin() {
        for (int i = 0; i < n; i++) {
            if (!st[i]) {
                boolean flag = true;
                for (int j = 0; j < n; j++) {
                    if (!st[j] && d[j][i]) {
                        flag = false;
                        break;
                    }
                }
                if (flag) {
                        st[i] = true;
                        return (char) (i + 'A');
                    }
            }
        }
        return ' ';
    }

    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    public static void main(String[] args) throws IOException {
        while (true) {
            String[] s1 = br.readLine().split(" ");
            n = Integer.parseInt(s1[0]);
            m = Integer.parseInt(s1[1]);
            if (n == 0 && m == 0) break;

            // 每出现一个新的关系做一次基于现在的新的floyd
            // 故需要有一个记载全局关系的数组 g[][] 每次大循环进行更新
            for (int i = 0; i < n; i++) Arrays.fill(g[i], false);

            //      type = 0 -> 无法确定关系
            //      type = 1 -> 有唯一解
            //      type = 2 -> 有矛盾
            //      t -> 记载得出关系结论的轮次
            int type = 0, t = 0;
            for (int i = 0; i < m; i++) {
                String s2 = br.readLine();
                int a = s2.charAt(0) - 'A', b = s2.charAt(2) - 'A';
                // 默认小于的情况下 建立 a -> b 的边
                // 为了保证读入全部的表达式 确认关系的情况下 仍然需要读入关系式 进行传递闭包
                if (type == 0) {
                    g[a][b] = true;
                    floyd();
                    type = check();
                    if (type != 0) t = i+1;
                }
            }

            if (type == 2) System.out.printf("Inconsistency found after %d relations.\n", t);
            else if (type == 0) System.out.println("Sorted sequence cannot be determined.");
            else {
                Arrays.fill(st, false);
                String ans = "";
                for (int i = 0; i < n; i++) ans += getMin();
                System.out.printf("Sorted sequence determined after %d relations: %s.\n", t, ans);
            }
        }
    }
}
```