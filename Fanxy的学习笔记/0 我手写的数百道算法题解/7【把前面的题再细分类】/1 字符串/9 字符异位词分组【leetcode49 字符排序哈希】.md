# <font color='bb000'>字符异位词分组【leetcode49 字符排序哈希】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你一个字符串数组，请你将 **字母异位词** 组合在一起。可以按任意顺序返回结果列表。

**字母异位词** 是由重新排列源单词的所有字母得到的一个新单词。

 

**示例 1:**

```
输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
输出: [["bat"],["nat","tan"],["ate","eat","tea"]]
```

**示例 2:**

```
输入: strs = [""]
输出: [[""]]
```

**示例 3:**

```
输入: strs = ["a"]
输出: [["a"]]
```

 

**提示：**

- `1 <= strs.length <= 10^4`
- `0 <= strs[i].length <= 100`
- `strs[i]` 仅包含小写字母



### 解析

这道题的特殊点在于，让每个字符串，能和异位字符串进行比较出相同？

找一个比较方法很麻烦，这样我们就相当于要进行 `O(n ^ 2)` 的比较，复杂度太高。

那么可以曲线救国，其实我们能找一个公共的第三方状态，能把每个字符串轻易和这个状态进行比较即可。

异位词说明每单个字符是相等的，如果我们把这些字符串按字符进行排序，即 `ascii` 码排序字符，那么异位词经过如上操作，一定可以变成相等的字符串。这样，以它作为唯一的 `key` 把原串映射到哈希表，哈希表存一个 `List` 存所有异位词。即可完成任务。



```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        List<List<String>> ans = new ArrayList<>();

        for (String str : strs) {
            char [] c = str.toCharArray();
            Arrays.sort(c);
            String s = new String(c);
            if (!map.containsKey(s)) map.put(s, new ArrayList<String>());
            map.get(s).add(str);
        }
        for(List list : map.values()) ans.add(new ArrayList(list));
        return ans;
    }
}
```



