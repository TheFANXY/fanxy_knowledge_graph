# <font color='bb000'>删除链表的倒数第Ｎ个结点【leetcode19 双指针】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 


## 题目介绍

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)

```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

**示例 2：**

```
输入：head = [1], n = 1
输出：[]
```

**示例 3：**

```
输入：head = [1,2], n = 1
输出：[1]
```

 

**提示：**

- 链表中结点的数目为 `sz`
- `1 <= sz <= 30`
- `0 <= Node.val <= 100`
- `1 <= n <= sz`

 

**进阶：**你能尝试使用一趟扫描实现吗？





### 方法一 ： 常规思考 先求长度 遍历两次

这里一个经典的判断数量关系和下标的方法，要么举例子，要么通过代数判断。

假设一共 `n` 个节点，那么倒数第 2 个节点就是正数第 `n - 1` 个，固分析出正数倒数的关系

```apl
倒数第 k 个  ----> = 正数第 n - k + 1 个
```

固只需要遍历两遍，第一遍获取链表长度，第二遍，遍历到对应节点，这里链表删除需要找到对应节点前面的节点，固只需要遍历到正数 `n - k` 个即可。

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
    public ListNode removeNthFromEnd(ListNode head, int k) {
        int n = 0;
        ListNode dummy = new ListNode(0, head);
        for (ListNode cur = dummy; cur.next != null; cur = cur.next) n ++;
        ListNode cur = dummy;
        for (int i = 0; i < n - k; i ++) cur = cur.next;
        cur.next = cur.next.next;
        return dummy.next;
    }
}
```

### 方法二 ： 只遍历一次

只遍历一次，那么肯定得双指针，那么该如何维护关系？

只需要满足最终指向删除前面的节点，按前面的推导即 `n - k` 正数的节点即可，那么需要另外一个指针和它的关系能维护一种关系，让指向答案的指针指到答案的时候，关系破裂即可。

链表出现遍历结束，一般都是走到结尾。

如果前面的指针指向 `n - k`，最后一个指针指到 `n`，此时就结束循环了。

即，后一个指针要比前一个多走 `k` 步。即我们先让一个指针走 `k` 步。然后再让第二个指针从第0个开始就绪，然后同时向后走，直到第二个指针指到最后一个节点即可。

```java
即满足最终 sentinel.next = null 时
---------
sentiel -> n
cur -> n - k
---------
那么当 sentinel -> k 的时候【即先走 k 步】根据差值
此时 sentinel 指针相当于变成 n - (n - k) = k
那么此时 cur 的指针要等值变化 (n - k) - (n - k) = 0 
即虚拟头节点    
```

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
    public ListNode removeNthFromEnd(ListNode head, int k) {
        ListNode dummy = new ListNode(0, head);
        ListNode sentinel = dummy, cur = dummy;
        int index = 0;

        while (sentinel.next != null) {
            sentinel = sentinel.next;
            index ++;
            if (index > k) cur = cur.next;
        }
        cur.next = cur.next.next;

        return dummy.next;
    }
}
```



