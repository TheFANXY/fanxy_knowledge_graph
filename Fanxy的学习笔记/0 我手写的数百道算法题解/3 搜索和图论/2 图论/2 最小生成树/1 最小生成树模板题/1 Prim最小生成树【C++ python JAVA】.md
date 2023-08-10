# <font color='bb000'>Prim算法求最小生成树-三种语言实现全解析</font>
## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

# 思路

## 最小生成树问题（无向图）

### 1 Prim算法求最小生成树

**1.时间复杂度方面**
 复杂度为O(`n^2`) 如果采用堆优化的办法可以提升到O(`mlogn`)

**2.算法原理**

`prim`算法类似`Dijkstra`算法，dist`存储距离整棵树的距离，每次找到距离树最短点，如果最短点已经是无穷
直接返回为无穷，如果不为无穷，**将它的值加入答案中，然后再用它更新每个点到树的距离**

**3.算法细节**

唯一的不同，`Dijkstra`更新的是权值加最短点距离的值，因为算的是距离起点的距离。而Prim算法算的是距离当前生成树的距离，所以**取当前距树和距树最近点与自己权值的最小值**。

```java
for(int j=1; j<=n; j++)
    dist[j] = min(dist[j], g[t][j]);
st[t] = true;//每当更新完所有的点，就锁定这个点，下次找最近点就不会重复刷新
```

**二刷发现的一个问题**
之前`dijkstra`算法我们是进行`n-1`次外层循环，因为找到`n-1`个点去更新剩下的，边就全部最小化了，其实加一层更新不了什么边了。但是Prim算法要加上所有边权，虽然边都最小化了，但是我们没加最后连在最后一个点的边权，加一层循环把最后一个边的权重加上。

# 1. java

```java
import java.util.*;

public class Main {
    static int N = 510, n, m, max = 0x3f3f3f3f;
    static int g[][] = new int [N][N];
    static int d[] = new int [N];
    static boolean st[] = new boolean [N];
    
    public static void prim() {
        int res = 0;
        Arrays.fill(d, max);
        d[1] = 0;
        
        for(int i=0; i<n; i++) {
            int t = -1;
            
            for (int j=1; j<=n; j++)
                if (!st[j] && (t == -1 || d[j] < d[t]))
                    t = j;
            if (d[t] == max) {
                System.out.println("impossible");
                return;
            }        
            res += d[t];
            
            st[t] = true;
            for (int j=1; j<=n; j++) {
                d[j] = Math.min(d[j], g[t][j]);
            }
        }
        
        System.out.println(res);
    }
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        n = sc.nextInt();
        m = sc.nextInt();
        
        for (int i=0; i<=n; i++)
            Arrays.fill(g[i], max);
        
        while(m -- > 0) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            int c = sc.nextInt();
            g[a][b] = g[b][a] = Math.min(g[a][b], c);
        }
        
        prim();
    }
}
```

# 2.python3 代码
```python
N = 510
g = [[0x3f3f3f3f]*N for i in range(N)]
d = [0x3f3f3f3f] * N
st = [0] * N

def prim():
    d[1] = 0
    res = 0
    for i in range(n):
        t = -1
        for j in range(1, n+1):
            if not st[j] and (t == -1 or d[t] > d[j]):
                t = j
                
        st[t] = True
        if d[t] == 0x3f3f3f3f:
            print('impossible')
            return
        res += d[t]
        
        for j in range(1, n+1):
            d[j] = min(d[j], g[t][j])
    
    print(res)

if __name__ == '__main__':
    n, m = map(int, input().split())
    for i in range(m):
        a, b, c = map(int, input().split())
        g[a][b] = g[b][a] = min(g[a][b], c)
    
    prim()
```
# 3.C++ 代码
```c++
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 510, INF = 0x3f3f3f3f;
int dist[N], g[N][N];
int n, m;
bool st[N];

int prim()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;
    int res = 0;
    for(int i=0; i<n; i++)
    {
        int t = -1;
        for(int j=1; j<=n; j++)
            if(!st[j] && (t == -1 || dist[j] < dist[t]))
                t = j;
        if(dist[t] == INF)   return INF;
        res += dist[t];    
        for(int j=1; j<=n; j++)
            dist[j] = min(dist[j], g[t][j]);
        st[t] = true;
    }
    return res;
}

int main()
{
    scanf("%d%d", &n, &m);
    memset(g, 0x3f, sizeof g);
    
    while(m--)
    {
        int a, b, c;
        scanf("%d%d%d", &a, &b, &c);
        g[a][b] = g[b][a] = min(g[a][b], c);
    }
    
    int t = prim();
    if(t == INF) puts("impossible");
    else printf("%d", t);
    
    return 0;
}
```