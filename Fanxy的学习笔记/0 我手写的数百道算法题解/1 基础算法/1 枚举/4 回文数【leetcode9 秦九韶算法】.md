# <font color='bb000'>回文数【leetcode9 秦九韶算法】</font>


## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你一个整数 `x` ，如果 `x` 是一个回文整数，返回 `true` ；否则，返回 `false` 。

回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

- 例如，`121` 是回文，而 `123` 不是。

 

**示例 1：**

```
输入：x = 121
输出：true
```

**示例 2：**

```
输入：x = -121
输出：false
解释：从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
```

**示例 3：**

```
输入：x = 10
输出：false
解释：从右向左读, 为 01 。因此它不是一个回文数。
```

 

**提示：**

- `-231 <= x <= 231 - 1`

 

**进阶：**你能不将整数转为字符串来解决这个问题吗？



### 字符串版

很简单，String在Java里不可变，且不具备倒排，下标找元素也需要单独的 `charAt()` ，直接转化为 `StringBuilder` 直倒排然后判相等，这里题目已经说明为负数必 `false`，所以直接提前判。

```java
class Solution {
    public boolean isPalindrome(int x) {
        if (x < 0) return false;
        String s1 = String.valueOf(x);
        String s2 = new StringBuilder(s1).reverse().toString();
        return s1.equals(s2);
    }
}
```



### 秦九韶算法

和枚举的第二题，整数倒序没什么区别，那道题比这道题多了一个越界提前判断。

```java
class Solution {
    public boolean isPalindrome(int x) {
        if (x < 0) return false;
        int y = x;
        long res = 0;
        while (x > 0) {
            res = res * 10 + x % 10;
            x /= 10;
        }
        return y == res;
    }
}
```