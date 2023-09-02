# <font color="bb000">数组顺时针旋转【leetcode48 分步旋转】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给定一个 *n* × *n* 的二维矩阵 `matrix` 表示一个图像。请你将图像顺时针旋转 90 度。

你必须在 **原地** 旋转图像，这意味着你需要直接修改输入的二维矩阵。**请不要** 使用另一个矩阵来旋转图像。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/08/28/mat1.jpg)

```java
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[[7,4,1],[8,5,2],[9,6,3]]
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/08/28/mat2.jpg)

```java
输入：matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
输出：[[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```

 

**提示：**

- `n == matrix.length == matrix[i].length`
- `1 <= n <= 20`
- `-1000 <= matrix[i][j] <= 1000`





### 解析

如果没有原地限制，其实可以直接根据枚举找规律映射关系，把旧的位置的数放到新数组的地方【可以忽略原地交换导致，需要考虑顺序让先交换的状态影响到了后面的状态】。

这道题属于取巧题，为了完成原地的顺时针旋转【其他也同理】，可以先进行主对角线的交换，然后再进行竖对称轴的左右交换。

```java 
class Solution {

    static void swap(int [][] array, int x1, int y1, int x2, int y2) {
        int t = array[x1][y1];
        array[x1][y1] = array[x2][y2];
        array[x2][y2] = t;
    }

    public void rotate(int[][] array) {
        int n = array.length;
        // 斜线交换
        for (int i = 0; i < n; i ++) {
            for (int j = 0; j < i; j ++) {
                swap(array, i, j, j, i);
            }
        }

        // 左右交换
        for (int i = 0; i < n; i ++) {
            for (int j = 0; j < n / 2; j ++) {
                swap(array, i, j, i, n - 1 - j);
            }
        }
    }
}
```





