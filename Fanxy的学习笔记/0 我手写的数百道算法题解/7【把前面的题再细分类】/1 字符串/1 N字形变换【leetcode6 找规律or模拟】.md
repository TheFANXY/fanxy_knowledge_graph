# <font color='bb000'>N字形变换【leetcode6 找规律or模拟】</font>


## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

将一个给定字符串 `s` 根据给定的行数 `numRows` ，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 `"PAYPALISHIRING"` 行数为 `3` 时，排列如下：

```java
P   A   H   N
A P L S I I G
Y   I   R
```

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`"PAHNAPLSIIGYIR"`。

请你实现这个将字符串进行指定行数变换的函数：

```java
string convert(string s, int numRows);
```

 

**示例 1：**

```java
输入：s = "PAYPALISHIRING", numRows = 3
输出："PAHNAPLSIIGYIR"
```

**示例 2：**

```java
输入：s = "PAYPALISHIRING", numRows = 4
输出："PINALSIGYAHRPI"
解释：
P     I    N
A   L S  I G
Y A   H R
P     I
```

**示例 3：**

```java
输入：s = "A", numRows = 1
输出："A"
```

 

**提示：**

- `1 <= s.length <= 1000`
- `s` 由英文字母（小写和大写）、`','` 和 `'.'` 组成
- `1 <= numRows <= 1000`



## 解析

### 方法一 ：找规律

```java
1        7          13
2     6  8      12
3  5	 9   11  
4        10 
```

我们首先发现，每一行距离，是一个固定的数，因为本身字符的形状是规律的，故从一个位置平移到下一个相同位置的递增位置，显然满足等差公式。

不难看出，这个公差为 `2 * n - 2`，因为纵向距离为 `n - 1` ，而斜着走过去其实本质也是反着走了一遍纵向距离，故此时仍为 `n - 1`，故公差为 `2 * n - 2`。

但我们也能发现，当不为首行和末尾行的时候，会出现两个等差序列，一个起点在第一列，一个起点在第一个斜体上。我们第一列直接能用列索引表示，但是斜体列我们需要找到它的表示方法，显然我们能观察到随着 `横轴 i`的增加一格，斜体起点的纵坐标也会随之减小一格，故这个斜体起点和枚举的行 `i` 有关系，且公差和之前没区别。

不难推出，这个起点为 `2 * n - 2 - i`，故枚举第一行到最后一行，然后分别根据公差建立循环把字符加入答案，即可结束本题。

当然这道题还有两个点可以优化，一个是我们完全可以使用 `StringBuilder` 降低字符串常量池一直建立新对象的字符串变化操作。

其次就是公差显然在 `n == 1` 的时候会变成 0 ，就死循环了，应该把这个点优化。

```java
class Solution {
    public String convert(String s, int n) {
        int len = s.length();
        if (n == 1) return s;

        StringBuilder res = new StringBuilder("");
        for (int i = 0; i < n; i ++){
            if (i == 0 || i == n - 1) {
                for (int j = i; j < len; j += (n - 1) * 2){
                    res.append(s.charAt(j));
                }
            } else {
                for (int j = i, k = (n - 1) * 2 - i;
                 j < len || k < len;
                 j += (n - 1) * 2, k += (n - 1) * 2){
                    if (j < len) res.append(s.charAt(j));
                    if (k < len) res.append(s.charAt(k));
                }
            }
        }
        return res.toString();
    }
}
```

### 方法二 ：直接模拟

我们可以看出，不同的行分属于不同的集合，然后加入这个集合的顺序是索引顺序加入，所以我们完全可以给每个不同的行创建一个`StringBuilder`，然后按索引依次枚举整个字符串，然后加入对应的行集合，每加入一次，如果是向下遍历，就枚举下一个`StringBuilder`，如果到头了，就倒序遍历。

可以利用一个`flag`，为整型常量 1，控制它在越界的情况下取相反数，然后加两倍的`flag`【抵消越界多走的一格并把指针指到下一个位置】

```java
class Solution {
    public String convert(String s, int n) {
        if(n < 2) return s;
        ArrayList<StringBuilder> rows = new ArrayList<>();
        for (int i=0; i<n; i++) rows.add(new StringBuilder(""));
        int i = 0, flag = 1;
        for (char c : s.toCharArray()) {
            rows.get(i).append(c);
            i += flag;
            if (i == -1 || i == n) {
                flag = -flag;
                i += 2 * flag;
            }
        }
        StringBuilder res = new StringBuilder("");
        for (StringBuilder line : rows) res.append(line);
        return res.toString();
    }
}
```

