# <font color='bb000'>括号生成【leetcode22 dfs单种括号】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

 

**示例 1：**

```
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```

**示例 2：**

```
输入：n = 1
输出：["()"]
```

 

**提示：**

- `1 <= n <= 8`



### 解析

这里其实上道括号题，即 `leetcode 20` 有效的括号，大致能总结出的结论能精简为：

1. 一个合法的括号序列的**中间状态**，左括号的数量一定大于等于右括号的数量
2. 一个**最终合法**的括号序列，左右括号的数量一定相等

根据上面的条件进行 `dfs` 搜索即可

下面是 使用 `StringBuilder` 的方法，鉴于 `StringBuilder` 的拼接是在原串基础上的，故需要手动回溯。

```java
class Solution {

    public static List<String> ans = new ArrayList<String>();

    public static void dfs(int n, int l, int r, StringBuilder s){
        if (l == n && r == n) {
            ans.add(s.toString());
            return;
        }
        if (l < n) {
            dfs(n, l + 1, r, s.append('('));
            s.deleteCharAt(s.length() - 1);    
        }
        if (r < l) {
            dfs(n, l, r + 1, s.append(')'));
            s.deleteCharAt(s.length() - 1);
        }
        
    }

    public List<String> generateParenthesis(int n) {
        ans.clear();
        dfs(n, 0, 0, new StringBuilder(""));
        return ans;
    }
}
```

以下是使用 String 的方法，在频繁进行字符串拼接的情况下，效率不高，会不停 new 对象，但是算法题里看着精简很多，而且我们还不需要手动回溯。

```java
class Solution {

    public static List<String> ans = new ArrayList<String>();

    public static void dfs(int n, int l, int r, String s){
        if (l == n && r == n) {
            ans.add(s);
            return;
        }
        if (l < n) dfs(n, l + 1, r, s + "("); 
        if (r < l) dfs(n, l, r + 1, s + ")");
    }

    public List<String> generateParenthesis(int n) {
        ans.clear();
        dfs(n, 0, 0, "");
        return ans;
    }
}
```

