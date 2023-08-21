# <font color='bb000'>K 个一组翻转链表【leetcode25 深度解析过程(三指针)】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你链表的头节点 `head` ，每 `k` 个节点一组进行翻转，请你返回修改后的链表。

`k` 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 `k` 的整数倍，那么请将最后剩余的节点保持原有顺序。

你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/10/03/reverse_ex1.jpg)

```
输入：head = [1,2,3,4,5], k = 2
输出：[2,1,4,3,5]
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/10/03/reverse_ex2.jpg)

```
输入：head = [1,2,3,4,5], k = 3
输出：[3,2,1,4,5]
```

 

**提示：**

- 链表中的节点数目为 `n`
- `1 <= k <= n <= 5000`
- `0 <= Node.val <= 1000`

 

**进阶：**你可以设计一个只用 `O(1)` 额外内存空间的算法解决此问题吗？



### 解析

这道题需要深刻把反转链表和上一道题，两两反转链表的节点两道题深刻体会，才能轻松拿捏。

首先反转链表的迭代版，教给我们反转操作，需要存临时节点，保证倒序之后断链的情况下，还能完成指针移位。

两两反转节点这道题，教会了我们需要多用一个指针指向每组的头节点前一个元素，这里称之为 `pre`，通过它的位置变动，能控制后面一组的节点位置在哪，同时可以通过 `pre` 后面是否有足够的不为空的节点数量，判断还需不需要进行位置变换操作。而这道题只进行两个节点的倒排，所以不需要存 `temp` ，让反转的双指针移位。

这道题从两个节点，变成了 `k` 个节点，所以相当于找寻 N 组满足 `k` 个节点的节点组，对内部的元素进行反转链表操作，所以反转链表部分需要保存 `temp` 。

同时两个节点的反转，我们的 三个指针 `pre` ，`sentinel` ， `cur` 覆盖了移位的所有元素，不需要引入 `temp` ，就能完成每组的收尾工作，让 `pre` 移位，同时让反转的链表接入原链表。

```sh
-1 -> 1 -> 2 -> 3 -> 4 -> 5 -> *
^	  ^	   ^
|	  |	   |
pre   cur  sentinel
```

但是这道题我们 k 个节点，需要 进行 k - 1 次 反转，这个时候想要完成接入原链表的操作，就还得存一下 `pre.next` 。具体细节如下。

```sh
-1 -> 1 <-> 2 <- 3    4 -> 5 -> *
^	  			 ^	  ^
|	  			 |	  |
pre   			cur  sentinel
```

这是完成 k - 1 次反转链表之后的样子，此时需要首先让 这组的第一个元素，即每次反转开局上面图中的 `cur` 指向的 1 号节点不指向 2 ，而是指向当前 `sentinel` 节点。完成第一步接入原链表，变成下图。

```sh
	  ----------------
	  |              |
	  |	             V
-1 -> 1 <- 2 <- 3    4 -> 5 -> *
^	  			^	 ^
|	  			|	 |
pre   		   cur  sentinel
```

下一步显然需要让前面的链表也指向反转的链表【此时的前面的链表只有 `pre` 指向的虚拟头节点，即让 `-1` 指向 `cur` 指针指向的 `3` 】。但是我们多考虑一下，最后肯定需要进行 `pre` 指针的位置移动，需要指向下一组需要反转的链表的第一个元素的前一个元素，即目前反转链表的第一个节点，上图应该是 `1`，但是问题来了，我们先进行前面的 `-1` 指向 `cur` ，就没有引用让 `pre` 最后移位到 `1` ，这也是为什么必须先用一个 `temp` 存一下 `pre.next` 地址的原因。

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
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode dummy = new ListNode(0, head);
        ListNode pre = dummy;
        while (pre != null) {
            ListNode q = pre;
            for (int i = 0; i < k && q != null; i ++) q = q.next;
            if (q == null) break;

            ListNode cur = pre.next;
            ListNode sentinel = cur.next;
            for (int i = 0; i < k - 1; i ++) {
                ListNode temp = sentinel.next;
                sentinel.next = cur;
                cur = sentinel;
                sentinel = temp;
            }

            // 交换 k - 1 次后 让反转的链表接入后面的链表 并改变 pre 指针的位置
            ListNode temp = pre.next;
            temp.next = sentinel;
            pre.next = cur;
            pre = temp;
        }
        return dummy.next;
    }
}
```

