# <font color='red'>KMP字符串 手动模拟 + 观测法帮助理解代码</font>
## **`我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

## 思考

**首先搞清楚前缀和后缀的含义**

字符串的前缀：符号串左部的任意子串（或者说是字符串的任意首部）

字符串的后缀：符号串右部的任意子串（或者说是字符串的任意尾部）

**以及明白观察法目测出与从起点1开始的最长前缀匹配的最长的j结尾后缀**

如 `abababab` 观察法去找寻`ne`数组形成
`ne[j]`数组指的是，当指针指到模板串`j`，模板串`j+1`去和要匹配的字符串的`i`比对，如果字符不同，应该移动到的下标值，也就是新的`j`

```apl
按长度枚举
前缀 a 后缀 b 不匹配
前缀 ab 后缀 ab 匹配
前缀 aba 后缀 bab 不匹配
前缀 abab 后缀 abab 匹配
前缀 ababa 后缀 babab 不匹配
前缀 ababab 后缀 ababab 匹配
前缀 abababa 后缀bababab 不匹配

那这个串最长的前后缀匹配就是ababab
```

**下面来看看具体求ne数组 枚举的时候变化**
```java
        for (int i = 2, j = 0; i <= p.length; i++) {
            while(j != 0 && p[i] != p[j+1]) j = ne[j];
            if(p[i] == p[j+1]) j++;
            ne[i] = j;
        }
```
**为什么`i`一开始是2，j是0？**
**首先这个方法不计入平凡串**，`ne[1]` 相当于单个字符匹配的情况下，因为`ne[1] = 0`即当第二位不匹配，第一位匹配的情况，找寻已经匹配的长度为1的串的最长前后缀匹配下标，长度为1，哪来的前缀后缀。所以默认从`j = 0`，即从第一位重新去匹配。
那`j = 0`，就可以理解了，因为从循环角度去考虑我们就是从第二位开始去进行同一个串的前后缀匹配，即

```java
 i
 |
abababab
 abababab
||
j|
 j+1
```

```apl
ne[1] = 0;
//----以上默认赋值-----

ne[2] = 0; 
//即当第3位不匹配，的最长的公共前缀 后缀 此时 前2位匹配 也就是满足 ab == ab 
//下面去找满足 ab 串的最长前后缀匹配
//显然 前缀 a 后缀 b 不匹配 j 只能跳转到0 即下次从s[1] = a 去匹配s[i]

ne[3] = 1;
//即当第4位不匹配，的最长的公共前缀 后缀 此时 前3位匹配 也就是满足 aba == aba
//下面去找满足 aba 串的最长前后缀匹配
//显然 前缀 a 后缀 a 匹配 可以跳转到 j = 1 即下次从s[2] = b 去匹配s[i]

ne[4] = 2;
//即当第5位不匹配，的最长的公共前缀 后缀 此时 前4位匹配 也就是满足 abab == abab
//下面去找满足 abab 串的最长前后缀匹配 
//显然 前缀 ab 后缀 ab 匹配 可以跳转到 j = 2 即下次从s[3] = a 去匹配s[i]

ne[5] = 3;
//即当第6位不匹配，的最长的公共前缀 后缀 此时 前5位匹配 也就是满足 ababa == ababa
//下面去找满足 ababa 串的最长前后缀匹配 
//显然 前缀 aba 后缀 aba 匹配 可以跳转到 j = 3 即下次从s[4] = b 去匹配s[i]


//----以下类似-----
```


融合记忆`ne`数组的生成——》`ne[1] = 0`；同时为什么`s[i] != p[j+1]`的情况下，进行`j = ne[j]`的操作——》因为默认此时`p[1-j]`已经和`s[1 - i-1]`匹配

# java
```java
import java.io.*;

public class Main {
    static int N = (int)1e5 + 10;
    static int [] ne = new int[N];
    
    public static void main(String [] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
        
        int n = Integer.parseInt(br.readLine());
        String s1 = " " + br.readLine();
        char [] s = s1.toCharArray();
        int m = Integer.parseInt(br.readLine());
        String s2 = " " + br.readLine();
        char [] p = s2.toCharArray();
        
        // ne数组生成 借助自己与自己的kmp比较
        for (int i=2, j=0; i<=n; i++) {
            while (j!=0 && s[i] != s[j+1]) j = ne[j];
            if (s[i] == s[j+1]) j++;
            ne[i] = j;
        }
        // kmp比较
        for (int i=1, j=0; i<=m; i++) {
            while (j!=0 && p[i] != s[j+1]) j = ne[j];
            if (p[i] == s[j+1]) j++;
            if (j == n) {
                j = ne[j];
                bw.write(i - n + " ");
            }
        }
        
        bw.flush();
    }
}
```

# python3
```python
N = int(100010)
ne = [0] * N

n = int(input())
p = '_' + input()
m = int(input())
s = '_' + input()
#ne数组生成
j = 0
for i in range(2, n+1):
    while j and p[i] != p[j+1]:  j = ne[j]
    if p[i] == p[j+1]: j += 1
    ne[i] = j
#kmp
j = 0
for i in range(1, m+1):
    while j and s[i] != p[j+1]:  j = ne[j]
    if s[i] == p[j+1]: j += 1
    if j == n:
        j = ne[j]
        print(i - n, end = ' ')
```
# C++
```c++
#include <iostream>
using namespace std;

const int N = 1e5 + 10, M = 1e6 + 10;
char p[N], s[M];
int ne[N];//存放最长公共前后缀的前缀末位下标
int n, m;

int main()
{
    cin >> n >> p + 1 >> m >> s + 1;
    //next数组形成
    for(int i=2, j=0; i<=n; i++)
    {
        while(j && p[i] != p[j+1])  j = ne[j];
        if(p[i] == p[j+1])  j++;
        ne[i] = j;
    }
    //kmp搜索
    for(int i=1, j=0; i<=m; i++)
    {
        while(j && s[i] != p[j+1])  j = ne[j];
        if(s[i] == p[j+1])          j ++;
        //匹配成功
        if(j == n)
        {
            printf("%d ", i - n);
            j = ne[j];
        }
    } 
    return 0;
}
```