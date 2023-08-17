# <font color='bb000'>电话号码的字母组合【leetcode17 dfs字符串】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2021/11/09/200px-telephone-keypad2svg.png)

**示例 1：**

```
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

**示例 2：**

```
输入：digits = ""
输出：[]
```

**示例 3：**

```
输入：digits = "2"
输出：["a","b","c"]
```

 

**提示：**

- `0 <= digits.length <= 4`
- `digits[i]` 是范围 `['2', '9']` 的一个数字。



### 解析

**简单爆搜，但是使用到了 `StringBuilder`，和 String 不同，就需要恢复现场，因为 `append` 操作是在原串基础上做的，而 `String` 的相加操作，面向对象学过，String 在 java 里是不可变的，即这个操作会重新 new 一个对象。**

**同时这里因为答案是由全局静态常量进行储存，而 `leetcode`  进行多组题解是不重新开始编译运行【java虚拟机启动太慢】，是一次性把多组进行测试，故每次都需要清理一次答案数组。**

```java
class Solution {
    static HashMap<String, String> map = new HashMap<>();
    static {
        map.put("2", "abc");
        map.put("3", "def");
        map.put("4", "ghi");
        map.put("5", "jkl");
        map.put("6", "mno");
        map.put("7", "pqrs");
        map.put("8", "tuv");
        map.put("9", "wxyz");
    }

    static List<String> ans = new ArrayList<>();

    static void dfs(String digits, int index, StringBuilder res) {
        if (index == digits.length()) {
            ans.add(res.toString());
            return;
        }

        String t = map.get(digits.substring(index, index + 1));
        for (int i = 0; i < t.length(); i ++) {
            dfs(digits, index + 1, res.append(t.charAt(i)));
            // 恢复现场
            res.deleteCharAt(res.length() - 1);
        }
    }

    public List<String> letterCombinations(String digits) {
        ans.clear();
        int n = digits.length();
        if (n == 0) return ans;
        dfs(digits, 0, new StringBuilder(""));
        return ans;
    }
}
```











