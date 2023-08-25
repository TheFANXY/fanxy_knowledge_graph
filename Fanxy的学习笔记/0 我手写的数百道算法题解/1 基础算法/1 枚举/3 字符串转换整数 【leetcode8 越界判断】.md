# <font color='bb000'>字符串转换整数 【leetcode8 越界判断】</font>


## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

请你来实现一个 `myAtoi(string s)` 函数，使其能将字符串转换成一个 32 位有符号整数（类似 C/C++ 中的 `atoi` 函数）。

函数 `myAtoi(string s)` 的算法如下：

1. 读入字符串并丢弃无用的前导空格
2. 检查下一个字符（假设还未到字符末尾）为正还是负号，读取该字符（如果有）。 确定最终结果是负数还是正数。 如果两者都不存在，则假定结果为正。
3. 读入下一个字符，直到到达下一个非数字字符或到达输入的结尾。字符串的其余部分将被忽略。
4. 将前面步骤读入的这些数字转换为整数（即，"123" -> 123， "0032" -> 32）。如果没有读入数字，则整数为 `0` 。必要时更改符号（从步骤 2 开始）。
5. 如果整数数超过 32 位有符号整数范围 `[−231, 231 − 1]` ，需要截断这个整数，使其保持在这个范围内。具体来说，小于 `−231` 的整数应该被固定为 `−231` ，大于 `231 − 1` 的整数应该被固定为 `231 − 1` 。
6. 返回整数作为最终结果。

**注意：**

- 本题中的空白字符只包括空格字符 `' '` 。
- 除前导空格或数字后的其余字符串外，**请勿忽略** 任何其他字符。

 

**示例 1：**

```java
输入：s = "42"
输出：42
解释：加粗的字符串为已经读入的字符，插入符号是当前读取的字符。
第 1 步："42"（当前没有读入字符，因为没有前导空格）
         ^
第 2 步："42"（当前没有读入字符，因为这里不存在 '-' 或者 '+'）
         ^
第 3 步："42"（读入 "42"）
           ^
解析得到整数 42 。
由于 "42" 在范围 [-231, 231 - 1] 内，最终结果为 42 。
```

**示例 2：**

```java
输入：s = "   -42"
输出：-42
解释：
第 1 步："   -42"（读入前导空格，但忽视掉）
            ^
第 2 步："   -42"（读入 '-' 字符，所以结果应该是负数）
             ^
第 3 步："   -42"（读入 "42"）
               ^
解析得到整数 -42 。
由于 "-42" 在范围 [-231, 231 - 1] 内，最终结果为 -42 。
```

**示例 3：**

```java
输入：s = "4193 with words"
输出：4193
解释：
第 1 步："4193 with words"（当前没有读入字符，因为没有前导空格）
         ^
第 2 步："4193 with words"（当前没有读入字符，因为这里不存在 '-' 或者 '+'）
         ^
第 3 步："4193 with words"（读入 "4193"；由于下一个字符不是一个数字，所以读入停止）
             ^
解析得到整数 4193 。
由于 "4193" 在范围 [-231, 231 - 1] 内，最终结果为 4193 。
```

 

**提示：**

- `0 <= s.length <= 200`
- `s` 由英文字母（大写和小写）、数字（`0-9`）、`' '`、`'+'`、`'-'` 和 `'.'` 组成



### 方法一 使用 long 【题目要求其实不可以】

这里首先对于字符串的判空，应该先判长度再判对应位置，要不然可能会出现通过索引找空串，直接越界。

其次这里为了避免出现对于 `int` 下界要比上界多一格，我们直接判范围提前把 `op` 乘积纳入判范围，省却出现提前判，出现刚好等于下界却被返回的错误边界情况。

```java
class Solution {
    public int myAtoi(String s) {
        int k = 0;
        while (k < s.length() && s.charAt(k) == ' ') k ++;
        if (k == s.length()) return 0;
        
        int op = 1;
        if (s.charAt(k) == '-') {
            op = -1;
            k ++;
        } else if (s.charAt(k) == '+') k ++;
        
        long res = 0;
        while (k < s.length() && s.charAt(k) >= '0' && s.charAt(k) <= '9') {
            int x = s.charAt(k) - '0';
            res = res * 10 + x;
            if (op > 0 && op * res >= Integer.MAX_VALUE) return Integer.MAX_VALUE;
            if (op < 0 && op * res <= Integer.MIN_VALUE) return Integer.MIN_VALUE;
            k ++;
        }
        return (int) (op * res);
    }
}
```

### 方法二 越界前判断范围

这里严格题目和上道题类似，即整数倒序，为了不爆 `int ` ，答案越界，但是乘数不越界，用乘数范围判乘积，就可以防止出现使用 `int` 之外的数。

**当 op 大于 0，即求正数，越界时满足**

```java
op * (res * 10 + x) > Integer.MAX_VALUE 
op * res > (Integer.MAX_VALUE - op * x) / 10     
```

**当 op 小于 0，即求负数，越界时满足**

```java
op * (res * 10 + x) < Integer.MIN_VALUE
op * res < (Integer.MIN_VALUE - op * x) / 10    
```

**其余情况下不越界，可以直接正常求解。**

```java
class Solution {
    public int myAtoi(String s) {
        int k = 0;
        while (k < s.length() && s.charAt(k) == ' ') k ++;
        if (k == s.length()) return 0;

        int op = 1;
        if (s.charAt(k) == '-') {
            k ++;
            op = -1;
        } else if(s.charAt(k) == '+') k ++;
        
        int res = 0;
        while (k < s.length() && s.charAt(k) >= '0' && s.charAt(k) <= '9') {
            int x = s.charAt(k) - '0';
            if (op > 0 && op * res > (Integer.MAX_VALUE - op * x) / 10) return Integer.MAX_VALUE;
            if (op < 0 && op * res < (Integer.MIN_VALUE - op * x) / 10) return Integer.MIN_VALUE;
            res = res * 10 + x;
            k ++;
        }
        return op * res;
    }
}
```

