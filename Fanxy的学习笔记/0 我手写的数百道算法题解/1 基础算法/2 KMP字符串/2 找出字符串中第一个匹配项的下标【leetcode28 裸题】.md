# <font color='red'>找出字符串中第一个匹配项的下标【leetcode28 裸题】</font>

## **`我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你两个字符串 `haystack` 和 `needle` ，请你在 `haystack` 字符串中找出 `needle` 字符串的第一个匹配项的下标（下标从 0 开始）。如果 `needle` 不是 `haystack` 的一部分，则返回 `-1` 。

 

**示例 1：**

```
输入：haystack = "sadbutsad", needle = "sad"
输出：0
解释："sad" 在下标 0 和 6 处匹配。
第一个匹配项的下标是 0 ，所以返回 0 。
```

**示例 2：**

```
输入：haystack = "leetcode", needle = "leeto"
输出：-1
解释："leeto" 没有在 "leetcode" 中出现，所以返回 -1 。
```

 

**提示：**

- `1 <= haystack.length, needle.length <= 104`
- `haystack` 和 `needle` 仅由小写英文字符组成



### 解析

裸题，直接用 `kmp` 即可

```java
class Solution {
    public int strStr(String haystack, String needle) {
        if (haystack == null || needle == null) return 0;
        int n = needle.length(), m = haystack.length();

        String s = " " + needle;
        String p = " " + haystack;
        int [] ne = new int [n + 10];

        for (int i = 2, j = 0; i <= n; i ++) {
            while (j != 0 && s.charAt(i) != s.charAt(j + 1)) j = ne[j];
            if (s.charAt(i) == s.charAt(j + 1)) j ++;
            ne[i] = j;
        }

        for (int i = 1, j = 0; i <= m; i ++) {
            while (j != 0 && p.charAt(i) != s.charAt(j + 1)) j = ne[j];
            if (p.charAt(i) == s.charAt(j + 1)) j ++;
            if (j == n) return i - n;
        }

        return -1;
    }
}
```



