# <font color='red'>三种语言=区别于kmp的dp</font>

## **`我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

----------
给定两个长度分别为 N 和 M 的字符串 A 和 B，求既是 A 的子序列又是 B 的子序列的字符串长度最长是多少。

**输入格式**

第一行包含两个整数 N 和 M


第二行包含一个长度为 N 的字符串，表示字符串 A。

第三行包含一个长度为 M 的字符串，表示字符串 B。

字符串均由小写字母构成。

**输出格式**

输出一个整数，表示最大长度。

数据范围
```
1 ≤ N  M ≤ 1000
```
**输入样例：**
```
4 5
acbd
abedc
```
**输出样例：**
```
3
```
----------

## 1.算法细节

*  **和kmp的区别？**

kmp问题应用在求一个更短串在一个更长串中是否出现的问题，优化了原本的`O (n * m)`的复杂度
而这题是不连续的子序列，没法使用当不匹配就换为最短公共前缀的思路去继续配对

*  **本题的dp划分区域重合对于实际情况有何影响？我们平时的DP应该如何划分？**

划分：`f[i][j]` 所有在第一个序列的前**`i`**个字母中出现，并且在第二个序列的前**`j`**个字母出现的子序列长度
1. **`f[i - 1][j - 1]` (未匹配的情况)**　
2. **`f[i - 1][j]` **
3. **`f[i][j - 1]` **
4. **`f[i - 1][j - 1] + 1`(当a[i] == b[j])**

**思考2和3的实际含义**：**如2.并不是我们想当然认为的a[i - 1]和b[j]之前均匹配的含义：而是在a[i -1]和b[j]这个范围内，能选到的最大序列长度**，**这个b[j]并不是一定能选到的**，那么实际应用就出现了范围扩大的尴尬情况。
但是这道题求的是最大值，尽管扩大了范围，但是我们要找寻的那个值也覆盖在其中，并且未匹配的情况也被2和3所涵盖了，我们求一个范围内的最大值，**哪怕产生了交集，但是只要我们可以覆盖完全整个区域，还是能求出最大值**

**`f[i - 1][j]` **代表着：`b[j]`出现后,`a[1 ~ i-1]和b[1 ~ j]`出现匹配的情况（已知`a[i] != b[j]`) **`我们可以理解成：一定不包含a[i]`**
**`f[i][j - 1]` **代表着：`b[j]`出现后,`a[1 ~ i]和b[1 ~ j-1]`出现匹配的情况（已知`a[i] != b[j]`) **`我们可以理解成：一定不包含b[j]`**

至于为什么不用在判断`f[i - 1][j - 1]`和他们谁更大是因为，这个情况已经被`f[i - 1][j]`和`f[i][j - 1]`覆盖了，不需要再去判断了。（从状态转移方程很容易理解）

**深刻理解：此前的`dp`已经保存最完美路径，我们需要考虑的是是否包含`a[i]`,`b[j]`故有三钟情况，根据是否`a[i] == b[j]`决定第三钟情况的全包含是否加1**

----------



## 2.具体代码

### java
```java
import java.io.*;
import java.util.*;

public class Main {
    
    static int N = 1010, n, m;
    static int [][] f = new int[N][N];
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    public static void main(String [] args) throws IOException{
        String [] s = br.readLine().split(" ");
        n = Integer.parseInt(s[0]);
        m = Integer.parseInt(s[1]);
        String a = " " + br.readLine();
        String b = " " + br.readLine();
        
        for (int i = 1; i <= n; i ++)
            for (int j = 1; j <= m; j ++) {
                f[i][j] = Math.max(f[i-1][j], f[i][j-1]);
                if (a.charAt(i) == b.charAt(j))
                    f[i][j] = Math.max(f[i][j], f[i-1][j-1] + 1);
            }
        System.out.println(f[n][m]);
    }
}
```

### python3 
```python
N = 1010
f = [[0] * N for i in range(N)]
n, m = map(int, input().split())
a = '_' + input()
b = '_' + input()

for i in range(1, n+1):
    for j in range(1, m+1):
        f[i][j] = max(f[i][j-1], f[i-1][j])
        if a[i] == b[j]:
            f[i][j] = max(f[i][j], f[i-1][j-1] + 1)

print(f[n][m])
```
### C++
```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;
int dp[N][N];
char a[N], b[N];
int n, m;

int main()
{
    cin >> n >> m >> a + 1 >> b + 1;
    
    for(int i=1; i<=n; i++)
        for(int j=1; j<=m; j++)
        {
            //由上可知，此类最大值一定包含在情况之中,未匹配也被涵盖了
            dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            //当相等时，加入特判必须纳入a[i] b[j]的选择法
            if(a[i] == b[j]) dp[i][j] = max(dp[i][j], dp[i - 1][j - 1] + 1);
        }
    
    cout << dp[n][m] << endl;
    return 0;
}
```