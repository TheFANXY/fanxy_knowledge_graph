# <font color='bb000'>Kruskal求最小生成树-三种语言实现全解析</font>
## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

# 思路
## Kruskal算法算最小生成树（并查集思想）

**1.时间复杂度方面**
时间复杂度 `O(mlogm)`

**2.算法原理**

**利用了并查集的思想**

**3.算法细节**
与Bellman_ford算法类似，需要进行全局的更新，但又没有指定点的位置，所以采用结构体存储，同一条树
采用并查集来维护，而排序工作只需要定义一个cmp用来比较权值，或者重载小于号,从头到尾遍历每个点，
用并查集合并生成树并统计树的边数

for循环遍历全部结构体存储的点👇
```java
a = find(a), b = find(b);
if(a != b){
    p[a] = b;
    cnt ++;//维护此时树的边数
    res += c;//维护最小生成树的边长总和
}
```
如果cnt小于n - 1即n个节点的最少边长，说明无法生成节点数为n的生成树，如果够则返回边的总长

# 1. java
```java
import java.util.*;
import java.io.*;

public class Main {
    static int N = 100010, M = 2 * N, n, m;
    static int p[] = new int [N];
    static Edge edges[] = new Edge [M];

    static class Edge {
        int a;
        int b;
        int w;
        public Edge(int a, int b, int w){
            this.a = a;
            this.b = b;
            this.w = w;
        }
    }

    public static int find(int x) {
        if (p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    public static void kruskal(){
        Arrays.sort(edges, 0, m, (o1, o2) -> Integer.compare(o1.w, o2.w));
        int res = 0;
        int cnt = 0;
        for(int i=0; i<m; i++) {
            int a = edges[i].a;
            int b = edges[i].b;
            int w = edges[i].w;
            int pa = find(a);
            int pb = find(b);
            
            if (pa != pb) {
                p[pa] = pb;
                res += w;
                cnt ++;
            }
        }

        if (cnt < n-1) System.out.println("impossible");
        else System.out.println(res);
    }

    public static void main(String [] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String s1[] = br.readLine().split(" ");
        n = Integer.parseInt(s1[0]);
        m = Integer.parseInt(s1[1]);
        for(int i=1; i<=n; i++) p[i] = i;

        for(int i=0; i<m; i++) {
            String s2[] = br.readLine().split(" ");
            int a = Integer.parseInt(s2[0]);
            int b = Integer.parseInt(s2[1]);
            int w = Integer.parseInt(s2[2]);
            edges[i] = new Edge(a, b, w);
        }
        kruskal();
    }
}
```

# 2. python3 代码
```python
N = int(1e5 + 10)
M = 2 * N
edge = []
p = [int(x) for x in range(N)]

def find(x):
    if p[x] != x:
        p[x] = find(p[x])
    return p[x]

def kruskal():
    global edge
    res = 0 #保存生成树长度
    cnt = 0 #保存生成树边数
    for i in range(m):
        a, b, w = edge[i]
        a = find(a)
        b = find(b)
        if a != b:
            p[a] = b
            cnt += 1
            res += w
    
    if cnt == n - 1:    return res
    else:    return "impossible"
    
if __name__ == '__main__':
    n, m = map(int, input().split())
    for i in range(m):
        a, b, c = map(int, input().split())
        edge.append([a, b, c])
    edge.sort(key = lambda x: x[2])
    
    print(kruskal())
```

# 3. C++ 代码
```C++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1e5 + 10, M = 2 * N, INF = 0x3f3f3f3f;
int n, m;
int p[N];

struct Edge
{
    int a, b, c;
//  重载小于号的方法
/*    
    bool operator < (const Edge &W)const
    {
        return c < W.c;
    }
*/    
}e[M];

//不重载小于号的从小到大排序的办法
bool cmp(struct Edge a, struct Edge b)
{
    return a.c < b.c;
}

int find(int x)
{
    if(p[x] != x)   p[x] = find(p[x]);
    return p[x];
}

int kruskal()
{
    sort(e, e + m, cmp);
    //若重载小于号
    //应写为sort(e, e + m);
    //并查集维护集合
    for(int i=1; i<=n; i++)  p[i] = i;
    
    //res存储树的权和，cnt存储当前树的总边数
    int res = 0, cnt = 0;
    for(int i=0; i<m; i++)
    {
        int a = e[i].a, b = e[i].b, c = e[i].c;
        
        a = find(a), b = find(b);
        if(a != b)
        {
            p[a] = b;
            cnt ++;
            res += c;
        }
    }
    //n个点能形成的最小生成树的边数为n-1
    if(cnt < n-1) return INF;
    return res;
}

int main()
{
    scanf("%d%d", &n, &m);
    for(int i=0; i<m; i++)
    {
        int a, b, c;
        scanf("%d%d%d", &a, &b, &c);
        e[i] = {a, b, c};
    }
    
    int t = kruskal();
    if(t == INF)    puts("impossible");
    else            printf("%d", t);
    
    return 0;
}
```