# <font color='bb000'>合并K个排序链表【leetcode22 堆替代归并】</font>

## **`下面的链接是——————我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

 

**示例 1：**

```apl
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

**示例 2：**

```apl
输入：lists = []
输出：[]
```

**示例 3：**

```apl
输入：lists = [[]]
输出：[]
```

 

**提示：**

- `k == lists.length`
- `0 <= k <= 10^4`
- `0 <= lists[i].length <= 500`
- `-10^4 <= lists[i][j] <= 10^4`
- `lists[i]` 按 **升序** 排列
- `lists[i].length` 的总和不超过 `10^4`





### 思路

如果当链表的数量达到了 N 个，还用归并最后就一个 循环之后，剩下的继续 if 判断，然后再次循环，代码冗余，且复杂度就高了。

不如用堆做简化，把每个链表的头节点先放入堆，然后每次取出最小的，然后插入答案的链表尾部，如果这个节点有下个节点，就放入堆。具体仍然要遍历所有节点，同时每次查询靠堆优化

**因此小根堆最多有k个元素，每次操作时，从小根堆取出堆顶元素，维护小根堆，时间复杂度是`O(logk)`，让取出来的元素的同链表中的下一个元素进入小根堆，维护小根堆，时间复杂度是`O(logk)`，总共有n个元素，因此最多操作n次`2O(logk)`的操作，因此总的时间复杂度是`O(2nlogk)`，即`O(nlogk)`**

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
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode dummy = new ListNode(0);
        ListNode cur = dummy;

        PriorityQueue <ListNode> q = new PriorityQueue<>((l1, l2) -> l1.val - l2.val);
        for(ListNode l : lists) if (l != null) q.offer(l);

        while (!q.isEmpty()) {
            ListNode top = q.poll();
            if (top.next != null) q.offer(top.next);
            cur.next = top;
            cur = cur.next;
        }

        return dummy.next;
    }
}
```