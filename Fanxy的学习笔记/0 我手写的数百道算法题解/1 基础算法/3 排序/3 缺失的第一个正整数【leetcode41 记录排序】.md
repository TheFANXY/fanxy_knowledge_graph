# <font color="bb000">缺失的第一个正整数【leetcode41 记录排序】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你一个未排序的整数数组 `nums` ，请你找出其中没有出现的最小的正整数。

请你实现时间复杂度为 `O(n)` 并且只使用常数级别额外空间的解决方案。

 

**示例 1：**

```
输入：nums = [1,2,0]
输出：3
```

**示例 2：**

```
输入：nums = [3,4,-1,1]
输出：2
```

**示例 3：**

```
输入：nums = [7,8,9,11,12]
输出：1
```

 

**提示：**

- `1 <= nums.length <= 5 * 105`
- `-231 <= nums[i] <= 231 - 1`



### 解析



#### 方法一 ： 【哈希表】不实现 `O(1)` 的空间复杂度

我们可以把所有的正数放入哈希表，然后从 `1` 开始遍历到 `n` 枚举第一个出现的正数，如果全部出现就返回 `n + 1`

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        int n = nums.length;
        for (int i = 0; i < n; i ++) {
            if (nums[i] > 0 && nums[i] <= n)
                map.put(nums[i], nums[i]);
        }
        for (int i = 1; i <= n; i ++) {
            if (map.getOrDefault(i, 0) == 0) 
                return i;
        }
        return n + 1;
    }
}
```



#### 方法二 ： 【记录排序】`O(1)` 空间复杂度

但是题目要求 `O(1)`  那么我们就只能在原地进行变换，即记录排序，让每个 `nums[i] = i + 1` 

然后再从头到尾遍历，是否满足这个条件，第一个不满足的就是答案，否则返回 `n + 1`

当然每次交换之后的数可能它也不能满足自己到对应位置，因此需要内部嵌套一个 `while` 循环，而如果两个数相等，就会出现死循环，题目没说数组是去重的，所以如果两数相等就不进行交换了。

```java 
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length;
        for (int i = 0; i < n; i ++) {
            while (nums[i] >= 1 && nums[i] <= n && nums[i] != nums[nums[i] - 1]) {
                int temp = nums[nums[i] - 1];
                nums[nums[i] - 1] = nums[i];
                nums[i] = temp;
            }
        } 
        for (int i = 0; i < n; i ++) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }
        return n + 1;
    }
}
```

