# <font color='bb000'>字符串相乘【leetcode43 字符串模拟】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给定两个以字符串形式表示的非负整数 `num1` 和 `num2`，返回 `num1` 和 `num2` 的乘积，它们的乘积也表示为字符串形式。

**注意：**不能使用任何内置的 `BigInteger` 库或直接将输入转换为整数。



**示例 1:**

```
输入: num1 = "2", num2 = "3"
输出: "6"
```

**示例 2:**

```
输入: num1 = "123", num2 = "456"
输出: "56088"
```

 

**提示：**

- `1 <= num1.length, num2.length <= 200`
- `num1` 和 `num2` 只能由数字组成。
- `num1` 和 `num2` 都不包含任何前导零，除了数字0本身。



### 解析

相当于高精度乘法，但是这里提供了一个思路，就是不要每次计算都进位，而是先把对应位的数算出来，最后统一进位操作。



```java
class Solution {
    public String multiply(String num1, String num2) {
        if (num1.equals("0") || num2.equals("0")) return "0";
        int n = num1.length();
        int m = num2.length();
        int [] a = new int [n];
        int [] b = new int [m];
        int [] s = new int [n + m];
        for (int i = n - 1; i >= 0; i --) a[n - 1 - i] = (int) (num1.charAt(i) - '0');
        for (int i = m - 1; i >= 0; i --) b[m - 1 - i] = (int) (num2.charAt(i) - '0');
        for (int i = 0; i < n; i ++)
            for (int j = 0; j < m; j ++) {
                s[i + j] += a[i] * b[j];
            }

        for (int i = 0, t = 0; i < n + m; i ++) {
            t += s[i];
            s[i] = t % 10;
            t /= 10;
        }
        int k = n + m - 1;
        StringBuilder ans = new StringBuilder("");
        while (k >= 0 && s[k] == 0) k --;
        while (k >= 0) ans.append(s[k--]);
        
        return ans.toString();
    }
}
```

