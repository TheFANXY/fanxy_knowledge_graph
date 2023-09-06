# <font color="bb000">两数相除【leetcode29 倍增】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你两个整数，被除数 `dividend` 和除数 `divisor`。将两数相除，要求 **不使用** 乘法、除法和取余运算。

整数除法应该向零截断，也就是截去（`truncate`）其小数部分。例如，`8.345` 将被截断为 `8` ，`-2.7335` 将被截断至 `-2` 。

返回被除数 `dividend` 除以除数 `divisor` 得到的 **商** 。

**注意：**假设我们的环境只能存储 **32 位** 有符号整数，其数值范围是 `[−2^31, 2^31 − 1]` 。本题中，如果商 **严格大于** `2^31 − 1` ，则返回 `2^31 − 1` ；如果商 **严格小于** `-2^31` ，则返回 `-2^31` 。

 

**示例 1:**

```
输入: dividend = 10, divisor = 3
输出: 3
解释: 10/3 = 3.33333.. ，向零截断后得到 3 。
```

**示例 2:**

```
输入: dividend = 7, divisor = -3
输出: -2
解释: 7/-3 = -2.33333.. ，向零截断后得到 -2 。
```

 

**提示：**

- `-231 <= dividend, divisor <= 231 - 1`
- `divisor != 0`



### 解析

这道题，题目的限制让我们只能使用减法加法，那么除法就只能是   `dividend = divisor * n + 余数` ，即循环减去 `divisor` ，但是极限情况， `dividend` = `2 ^ 31` ， `divisor = 1` ，复杂度达到了 `2 ^ 31` ，必定超时。

故需要想办法能优化。即 二进制压缩 。 我们把 `divisor * n` 压缩成 => 

`1 * divisor + 2 * divisor + 4 * divisor + .......... + 2x * divisor`

即把 n => log(n) ，相当于极限最大也就 `31` 个。那么就把  `O(n)` 的复杂度优化为 `O(log(n))`

而边界情况，是会出现爆 `int` ，即 `(- 2 ^ 31)` / `(-1)` ，由于 `int ` 最大只有 `2 ^ 31 - 1` ，所以需要提前特判，同时计算过程中我们需要转换为 `long` ，以消除各种麻烦的边界情况，最后转换为 `int`

```java
class Solution {
    public int divide(int x, int y) {
        long a = x, b = y;
        boolean flag = false;
        List<Long> exp = new ArrayList<>();
        
        if ((a < 0 && b > 0) || (a > 0 && b < 0)) flag = true;
        if (a < 0) a = -a;
        if (b < 0) b = -b;

        for (long i = b; i <= a; i = i + i) exp.add(i);
        
        long res = 0;

        for (int i = exp.size() - 1; i >= 0; i --) {    
            if (a >= exp.get(i)) {
                a -= exp.get(i);
                res += 1L << i;
            }
        }

        if (flag) res = - res;
        if (res >= Integer.MAX_VALUE) return Integer.MAX_VALUE;
        return (int) res;
    }
}
```











 