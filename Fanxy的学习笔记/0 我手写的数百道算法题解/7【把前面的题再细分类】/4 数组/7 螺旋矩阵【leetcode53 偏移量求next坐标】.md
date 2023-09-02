# <font color="bb000">螺旋矩阵【leetcode53 偏移量求next坐标】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 



给你一个 `m` 行 `n` 列的矩阵 `matrix` ，请按照 **顺时针螺旋顺序** ，返回矩阵中的所有元素。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/11/13/spiral1.jpg)

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/11/13/spiral.jpg)

```
输入：matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```

 

**提示：**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 10`

- `-100 <= matrix[i][j] <= 100`



### 解析

因为题目的移动保证能走完全部的点，所以就出现越界或者重复情况，就直接换成下一个偏移量的方向即可。

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        int [] dx = {-1, 0, 1, 0}, dy = {0, 1, 0, -1};
        List<Integer> ans = new ArrayList<>();
        int n = matrix.length, m = matrix[0].length;
        boolean [][] st = new boolean[n][m];
        int cur_x = 0, cur_y = 0, offset = 1;
       
        while (ans.size() < n * m) {
            st[cur_x][cur_y] = true;
            ans.add(matrix[cur_x][cur_y]);
            int ne_x = dx[offset] + cur_x;
            int ne_y = dy[offset] + cur_y;
            if (ne_x < 0 || ne_x >= n || ne_y < 0 || ne_y >= m || st[ne_x][ne_y]) {
                offset = (offset + 1) % 4;
                ne_x = dx[offset] + cur_x;
                ne_y = dy[offset] + cur_y;
            }
            cur_x = ne_x;
            cur_y = ne_y;
        }
        return ans;
    }
}
```



