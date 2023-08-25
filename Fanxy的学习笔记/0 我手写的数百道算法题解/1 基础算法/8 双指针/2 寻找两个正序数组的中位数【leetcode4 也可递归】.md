# <font color='bb000'>寻找两个正序数组的中位数【双指针/递归】 leetcode4</font>


## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 






给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的 **中位数** 。

算法的时间复杂度应该为 `O(log (m+n))` 。

 

**示例 1：**

```java
输入：nums1 = [1,3], nums2 = [2]
输出：2.00000
解释：合并数组 = [1,2,3] ，中位数 2
```

**示例 2：**

```java
输入：nums1 = [1,2], nums2 = [3,4]
输出：2.50000
解释：合并数组 = [1,2,3,4] ，中位数 (2 + 3) / 2 = 2.5
```

 

 

**提示：**

- `nums1.length == m`
- `nums2.length == n`
- `0 <= m <= 1000`
- `0 <= n <= 1000`
- `1 <= m + n <= 2000`
- `-106 <= nums1[i], nums2[i] <= 106`

## 解析

这题我第一直觉，双指针，时间复杂度O(k)，会遍历两个数组一共前 `k` 个元素。

类似归并排序里面，三个循环，大循环双指针，当一个数组满了，继续把另一个数组的遍历到指定下标。

如果是偶数就取中间两个数的平均值。

然后看了 `y总` 的递归解法，更优雅，每次可以省去 `k / 2`个数字，最后我测试我发现其实和我的双指针时间和空间复杂度区别不大，考虑到递归做法的边界判断太多，建议大家如果面试遇到，使用判断边界更少，更容易写出来的算法即可。

## 我的双指针算法

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int tot = nums1.length + nums2.length;

        if (tot % 2 != 0) {
            return (double) find(nums1, nums2, tot / 2 + 1);
        } else {
            int left = find(nums1, nums2, tot / 2 + 1);
            int right = find(nums1, nums2, tot / 2);
            return (left + right) / 2.0;
        }
    }

    public int find(int [] nums1, int [] nums2, int tot){
        int i = 0, j = 0;
        int cnt = 0;
        int ans = 0;
        while (i < nums1.length && j < nums2.length){
            if (nums1[i] <= nums2[j]) ans = nums1[i++];
            else ans = nums2[j++];
            cnt ++;
            if (cnt == tot) return ans;
        }
        while (i < nums1.length) {
            ans = nums1[i++];
            cnt ++;
            if (cnt == tot) return ans;
        }

        while (j < nums2.length) {
            ans = nums2[j++];
            cnt ++;
            if (cnt == tot) return ans;
        }
        return ans;
    }
}
```

## y总的递归思路

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int total = nums1.length + nums2.length;
        if(total % 2 == 0)
        {
            int left = f(nums1,0,nums2,0,total / 2);
            int right = f(nums1,0,nums2,0,total / 2 + 1);
            return (left + right) / 2.0;
        }
        else return f(nums1,0,nums2,0,total / 2 + 1);
    }
    static int f(int[] nums1,int i,int[] nums2,int j,int k)
    {
        //默认第一个是小的
        if(nums1.length - i > nums2.length - j) return f(nums2,j,nums1,i,k);
        //当第一个数组已经用完
        if(nums1.length == i) return nums2[j + k - 1];
        //当取第1个元素
        if(k == 1) return Math.min(nums1[i],nums2[j]);

        int si = Math.min(nums1.length,i + k / 2),sj = j + k - k / 2;
        if(nums1[si - 1] > nums2[sj - 1])
        {
            return f(nums1,i,nums2,sj,k - (sj - j));
        }
        else
        {
            return f(nums1,si,nums2,j,k - (si - i));
        }
    }
}
```