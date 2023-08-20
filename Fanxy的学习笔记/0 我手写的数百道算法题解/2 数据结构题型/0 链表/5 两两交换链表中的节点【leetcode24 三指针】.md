# <font color='bb000'>两两交换链表中的节点【leetcode24 三指针】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/10/03/swap_ex1.jpg)

```
输入：head = [1,2,3,4]
输出：[2,1,4,3]
```

**示例 2：**

```
输入：head = []
输出：[]
```

**示例 3：**

```
输入：head = [1]
输出：[1]
```

 

**提示：**

- 链表中节点的数目在范围 `[0, 100]` 内
- `0 <= Node.val <= 100`



### 解析

最开始想用双指针，然后发现头节点会变，然后就考虑了多种边界情况，最后感觉代码冗余太多，开局需要判断太多边界条件。

其实边界太多的双指针，要么理清边界的情况，要么可以先考虑一下，要不要加个指针。

我们把虚拟头节点作为第一个指针 `pre`，下一个节点 `cur` ，再下一个节点 `sentineel` ，这样就能把头节点变化，直接融入交换节点的过程了。

而能否进行交换，只需要判断 `pre` 节点后面有没有两个节点，没有的话就不遍历，非常完美的把最终边界也解决了。

最后就是交换顺序了。

显然需要交换的两个节点 `cur` 和 `sentineel` ，他们两个需要先让后面的指向前面的 `sentinel.next`，然后再让 `sentinel.next = cur`。

最后只需要把 `pre` 指针，指到此时位于末尾的节点，即 `cur` ，完美的解决了前后边界和中间移位置。

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode dummy = new ListNode(0, head);
        ListNode pre = dummy;
        
        while (pre.next != null && pre.next.next != null) {
            ListNode cur = pre.next;
            ListNode sentinel = cur.next;   
            pre.next = sentinel;
            cur.next = sentinel.next;
            sentinel.next = cur;
            pre = cur;
        }

        return dummy.next;
    }
}
```













