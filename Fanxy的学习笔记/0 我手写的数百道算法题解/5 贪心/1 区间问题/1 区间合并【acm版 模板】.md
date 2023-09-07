# 区间合并=三种语言PII版和贪心版

## **`我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给定 n 个区间 `[li, ri]`，要求合并所有有交集的区间。

注意如果在端点处相交，也算有交集。

输出合并完成后的区间个数。

例如：`[1,3]` 和 `[2,6]` 可以合并为一个区间 `[1,6]`。



**输入格式**

第一行包含整数 n。

接下来 n 行，每行包含两个整数 l 和 r。



**输出格式**

共一行，包含一个整数，表示合并区间完成后的区间个数。



**数据范围**

`1 ≤ n ≤ 100000`
`−10^9 ≤ li ≤ ri ≤10^9`



**输入样例：**

```
5
1 2
2 4
5 6
7 8
7 9
```

#### 输出样例：

```
3
```



### java 贪心模板
```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.Comparator;

public class Main {
    static int N = 100010, n;
    static int[][] edges = new int[N][2];
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());
        for (int i = 0; i < n; i++) {
            String[] s2 = br.readLine().split(" ");
            edges[i][0] = Integer.parseInt(s2[0]);
            edges[i][1] = Integer.parseInt(s2[1]);
        }
        Arrays.sort(edges, 0, n, Comparator.comparingInt(o -> o[0]));
        int l = -(int)2e9, r = -(int)2e9;
        int res = 0;
        for (int i = 0; i < n; i++) {
            if (edges[i][0] > r){
                if (r != (int)1e9) res ++;
                l = edges[i][0];
                r = edges[i][1];
            }else r = Math.max(r, edges[i][1]);
        }
        System.out.println(res);
    }
}
```



### java PII模板

```java
import java.util.*;
class P {
        int x, y;
        public P(int x, int y) {
            this.x = x;
            this.y = y;
    }
}

public class Main {
    public static void main(String [] args) {
        Scanner sc = new Scanner(System.in);
        
        int n = sc.nextInt();
        List <P> list = new ArrayList<>();
        for (int i=0; i<n; i++) list.add(new P(sc.nextInt(), sc.nextInt()));
        int ans = fun(list);
        System.out.print(ans);
    }
    
    public static int fun(List <P> list) {
        list.sort((A, B) -> A.x - B.x);
        List <P> ans = new ArrayList<>();
        int l = (int) -2e9, r = (int) -2e9;
        
        for (P e: list) {
            if (r < e.x) {
                if (r != (int) -2e9) ans.add(new P(l, r));
                l = e.x;
                r = e.y;
            }
            else r = Math.max(r, e.y);
        }
        if (r != (int) -2e9) ans.add(new P(l, r));
        return ans.size();
    }
}

```



### 方法1 C++: 区间合并模板

```c++
#include <iostream>
#include <vector>
#include <algorithm>
#define read(x) scanf("%d", &x)

using namespace std;

typedef pair<int,int> pii;
vector<pii> seg, res;

int main()
{
    int n, l, r;
    read(n);
    while(n--)
    {
        read(l), read(r);
        seg.push_back({l,r});
    }
    sort(seg.begin(), seg.end());
    int st = -2e9, ed = -2e9; 
    for(auto i : seg)
    {
        if(ed < i.first) 
        {
            if(ed != -2e9)  res.push_back({st,ed});
            st = i.first, ed = i.second;
        }
        else ed = max(ed, i.second);
    }
    //若最后一步是合并的时候，并未存入res之中，所以要特判一下
    if(ed != -2e9) res.push_back({st, ed}); 
    printf("%d", res.size());
    return 0;
}
```



### 方法二 C++ : 贪心模板

```c++
#include <iostream>
#include <algorithm>
using namespace std;
const int N = 1e5 + 10;

struct seg
{
    int l, r;
}s[N];

bool cmp(seg a, seg b)
{
    return a.l < b.l;
}

int main()
{
    int n;
    cin >> n;
    for(int i=0; i<n; i++)
    {
        int l, r;
        cin >> l >> r;
        s[i] = {l,r};
    }
    
    sort(s, s+n, cmp);
    int res = 0, st, ed = -2e9;
    for(int i=0; i<n; i++)
    {
        if(s[i].l > ed)
        {
            res ++;
            ed = s[i].r;
        }
        else    ed = max(s[i].r, ed);
    }
    
    cout << res << endl;
    
    return 0;
}
```


### 方法三 Python3 : 贪心模板

```python
n = int(input())
a = []
for i in range(n):
    l, r = map(int, input().split())
    a.append((l,r))
a.sort(key = lambda x: x[0])
ed = a[0][1]
res = 1
for i in range(1, n):
    if a[i][0] > ed:
        ed = a[i][1]
        res += 1
    else:
        ed = max(ed, a[i][1])
print(res)

```