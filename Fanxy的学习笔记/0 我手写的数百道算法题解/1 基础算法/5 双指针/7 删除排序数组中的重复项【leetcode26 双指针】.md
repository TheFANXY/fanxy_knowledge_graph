# <font color='bb000'>删除排序数组中的重复项【leetcode26 双指针】</font>


## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你一个 **升序排列** 的数组 `nums` ，请你**[ 原地](http://baike.baidu.com/item/原地算法)** 删除重复出现的元素，使每个元素 **只出现一次** ，返回删除后数组的新长度。元素的 **相对顺序** 应该保持 **一致** 。然后返回 `nums` 中唯一元素的个数。

考虑 `nums` 的唯一元素的数量为 `k` ，你需要做以下事情确保你的题解可以被通过：

- 更改数组 `nums` ，使 `nums` 的前 `k` 个元素包含唯一元素，并按照它们最初在 `nums` 中出现的顺序排列。`nums` 的其余元素与 `nums` 的大小不重要。
- 返回 `k` 。

**判题标准:**

系统会用下面的代码来测试你的题解:

```java
int[] nums = [...]; // 输入数组
int[] expectedNums = [...]; // 长度正确的期望答案

int k = removeDuplicates(nums); // 调用

assert k == expectedNums.length;
for (int i = 0; i < k; i++) {
    assert nums[i] == expectedNums[i];
}
```

如果所有断言都通过，那么您的题解将被 **通过**。



### 解析

这道题是经典 C++ 和 JAVA 数组判重底层的双指针实现，具体做法很简单，我们只需要一个指针 `k` 存未来要存的去重元素，一个指针 `i` 遍历全数组，一旦出现一个新的元素【和 `nums[i - 1]` 不相等】，就存到 `nums[k]` ，这个过程只需要特判初始点，因为它肯定要存，但是又不能一上来和 `nums[-1]` 判重，越界。

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int k = 0;
        for (int i = 0; i < nums.length; i ++)
            if (i == 0 || nums[i] != nums[i-1])
                nums[k++] = nums[i];

        return k;
    }
}
```

