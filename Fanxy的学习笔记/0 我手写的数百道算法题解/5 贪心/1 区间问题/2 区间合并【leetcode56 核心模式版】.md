# 区间合并【leetcode56 核心模式版】

以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]` 。请你合并所有重叠的区间，并返回 *一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间* 。

 

**示例 1：**

```
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

**示例 2：**

```
输入：intervals = [[1,4],[4,5]]
输出：[[1,5]]
解释：区间 [1,4] 和 [4,5] 可被视为重叠区间。
```

 

**提示：**

- `1 <= intervals.length <= 10^4`
- `intervals[i].length == 2`
- `0 <= starti <= endi <= 10^4`



## 解析

这题最主要的是如何把 `List` 集合，直接变成二维数组。其他的就是模板。 

```java
class Solution {
    public int[][] merge(int[][] edges) {
        List<int[]> ans = new ArrayList<>();
        Arrays.sort(edges, (o1, o2)-> o1[0] -o2[0]);

        int l = edges[0][0], r = edges[0][1];
        for (int i = 1; i < edges.length; i ++) {
            if (edges[i][0] > r) {
                ans.add(new int[]{l, r});
                l = edges[i][0];
                r = edges[i][1];
            } else {
                r = Math.max(r, edges[i][1]);
            }
        }
        ans.add(new int[]{l, r});
        return ans.toArray(new int[ans.size()][]);
    }
}
```

