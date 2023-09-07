# 插入区间【leetcode57 区间合并变体】

## **`我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

# 题目介绍

给你一个 **无重叠的** *，*按照区间起始端点排序的区间列表。

在列表中插入一个新的区间，你需要确保列表中的区间仍然有序且不重叠（如果有必要的话，可以合并区间）。

 

**示例 1：**

```
输入：intervals = [[1,3],[6,9]], newInterval = [2,5]
输出：[[1,5],[6,9]]
```

**示例 2：**

```
输入：intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
输出：[[1,2],[3,10],[12,16]]
解释：这是因为新的区间 [4,8] 与 [3,5],[6,7],[8,10] 重叠。
```

**示例 3：**

```
输入：intervals = [], newInterval = [5,7]
输出：[[5,7]]
```

**示例 4：**

```
输入：intervals = [[1,5]], newInterval = [2,3]
输出：[[1,5]]
```

**示例 5：**

```
输入：intervals = [[1,5]], newInterval = [2,7]
输出：[[1,7]]
```

 

**提示：**

- `0 <= intervals.length <= 104`
- `intervals[i].length == 2`
- `0 <= intervals[i][0] <= intervals[i][1] <= 105`
- `intervals` 根据 `intervals[i][0]` 按 **升序** 排列
- `newInterval.length == 2`
- `0 <= newInterval[0] <= newInterval[1] <= 105`



### 解析

这题其实直接根据区间合并的思路，模拟即可，把不在给出的新区间覆盖范围的区间直接加入答案，而出现的第一个产生交集，即右端点大于等于给出区间左端点的，然后不断把后续的左区间小于等于该区间右端点的区间一起合并，然后把剩余的不重叠归入答案即可。

这题也可以二分，直接找寻第一个 右端点大于等于给出区间左端点的区间的左端点，和该区间的左端点的两者最小值，作为合并区间左端点。

然后找寻左区间小于等于该区间右端点的区间，他们两者右端点最大值，作为合并区间的右端点。

然后枚举整个数组，把其他的区间归并，到中间的区间 `continue` 跳出循环。

```java
class Solution {
    public int[][] insert(int[][] a, int[] b) {
        List <int[]> res = new ArrayList<>();
        int k = 0;
        while (k < a.length && a[k][1] < b[0]) res.add(a[k++]);
        if (k < a.length) {
            b[0] = Math.min(a[k][0], b[0]);
            while (k < a.length && a[k][0] <= b[1]) b[1] = Math.max(b[1], a[k++][1]);
        }
        res.add(b);
        while (k < a.length) res.add(a[k++]);
        return res.toArray(new int[res.size()][]);
    }
}
```

