# <font color='bb000'>最长回文子串【双指针】 leetcode5</font>


## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你一个字符串 `s`，找到 `s` 中最长的回文子串。

如果字符串的反序与原始字符串相同，则该字符串称为回文字符串。

 

**示例 1：**

```apl
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```



**示例 2：**

```apl
输入：s = "cbbd"
输出："bb"
```



**提示：**

- `1 <= s.length <= 1000`
- `s` 仅由数字和英文字母组成



## 解析

这道题其实还可以二分 + hash 来做，时间复杂度更低，但是这里先说一下双指针做法，直觉上，可以枚举整个字符串的每个字符作为起点，然后用两个指针一个向左，一个向右，一旦两个指针所指的字符相等，就分别左移右移，然后不断维护一个最大的字符答案。

同时不能忘了存在奇数字符情况和偶数情况，奇数就以枚举点两边作为左右指针起点，偶数就以枚举点作为一个起点，相反方向一格的另一个字符作为另外一个起点。

从时间复杂度来看，外层应该是 `n` 次遍历，内层是两次，每次枚举层数也是最多为 `n`，而两次在常数面前可以省略，故综合时间复杂度为 `O(n ^ 2)`。

这里别忘了 `java`的 `substring`首先没有大写字母，其次 `subsring`  左闭右开区间，第二个参数不是长度。

```java
class Solution {
    public String longestPalindrome(String s) {
        if(s.length() <= 1) return s;
        String res = "";
        for (int i=0; i<s.length(); i++){
            int l = i - 1, r = i + 1;
            while (l >= 0 && r < s.length() && s.charAt(l) == s.charAt(r)){
                l --; 
                r ++;
            }
            if (res.length() < r - l - 1) res = s.substring(l + 1, r);
            
            l = i;
            r = i + 1;
            while (l >= 0 && r < s.length() && s.charAt(l) == s.charAt(r)){
                l --; 
                r ++;
            }
            if (res.length() < r - l - 1) res = s.substring(l + 1, r);
        }
      return res;
    }
}
```