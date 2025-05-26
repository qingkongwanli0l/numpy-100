# 2. 两数相加 (Add Two Numbers)

### 问题描述
给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。
[查看LeetCode问题描述](https://leetcode.cn/problems/add-two-numbers/)

### 最优解法思路
该问题可以通过模拟小学时学的手动加法过程来解决。我们从头到尾遍历两个链表（由于是逆序存储，这对应于从最低有效位到最高有效位）。我们维护一个 `carry`（进位）变量，初始化为 0。对于每个位置，我们将两个链表中的数字（如果可用）与前一个位置的 `carry` 相加。结果链表的新数字是和模 10 (`sum % 10`)，要传播到下一个位置的新 `carry` 是和除以 10 (`sum / 10`)。我们为和构造一个新的链表。可以使用一个虚拟头节点来简化附加第一个节点的操作。我们继续这个过程，直到两个输入链表都被完全遍历并且没有剩余的 `carry`。

### 解题步骤详解
1.  初始化一个值为 `0` 的 `dummyHead` 节点。该节点充当占位符，以简化添加第一个实际数字节点的操作。
2.  初始化一个 `current` 指针指向 `dummyHead`。该指针将始终指向结果列表中的最后一个节点，下一个新的数字节点将附加到该节点。
3.  初始化一个整数 `carry` 为 `0`。如果任何位置的数字之和超过 9，它将存储进位值。
4.  初始化两个指针，`p1` 指向第一个列表 (`l1`) 的头部，`p2` 指向第二个列表 (`l2`) 的头部。
5.  开始一个循环，只要 `p1` 不为 `null`，或者 `p2` 不为 `null`，或者 `carry` 不为 `0`，循环就继续。这确保了处理两个列表中的所有数字并处理任何最终的进位。
    a.  在循环内部，确定 `p1` 指向的节点的值 `x`。如果 `p1` 为 `null`（表示第一个列表较短或已用尽），则 `x` 为 `0`。
    b.  类似地，确定 `p2` 指向的节点的值 `y`。如果 `p2` 为 `null`，则 `y` 为 `0`。
    c.  计算当前位置的和：`sum = x + y + carry`。
    d.  更新下一个位置的 `carry`：`carry = sum / 10`。（整数除法得到进位）。
    e.  要存储在结果列表的新节点中的实际数字是 `sum % 10`。
    f.  使用此数字创建一个新的 `ListNode`：`current.next = new ListNode(sum % 10)`。
    g.  将 `current` 指针移动到这个新添加的节点：`current = current.next`。
    h.  如果 `p1` 不为 `null`，则将 `p1` 前进到其下一个节点：`if (p1 != null) p1 = p1.next;`。
    i.  如果 `p2` 不为 `null`，则将 `p2` 前进到其下一个节点：`if (p2 != null) p2 = p2.next;`。
6.  循环终止后，完整的和列表由 `dummyHead.next` 指向。返回 `dummyHead.next`。

### Java 代码实现
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val; // 存储此节点的数字值
 *     ListNode next; // 指向列表中下一个节点的指针
 *     ListNode() {} // 默认构造函数
 *     ListNode(int val) { this.val = val; } // 带值的构造函数
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; } // 带值和下一个节点的构造函数
 * }
 */
class Solution {
    // 方法：将由链表 l1 和 l2 表示的两个数字相加
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        // 创建一个虚拟头节点，以简化处理结果列表的开头。
        // 实际结果将是 dummyHead.next。
        ListNode dummyHead = new ListNode(0);

        // 初始化 'p' 以遍历 l1，'q' 以遍历 l2。
        ListNode p = l1;
        ListNode q = l2;
        // 'current' 将指向结果列表中的最后一个节点，用于附加新节点。
        ListNode current = dummyHead;

        // 初始化进位为 0。这将存储数字相加产生的进位。
        int carry = 0;

        // 只要 l1 中有数字，或 l2 中有数字，或有待处理的进位，就继续循环。
        while (p != null || q != null || carry != 0) {
            // 从 l1 的当前节点获取值。如果 p 为 null，则此列表暂时用尽，因此使用 0。
            int x = (p != null) ? p.val : 0;
            // 从 l2 的当前节点获取值。如果 q 为 null，则此列表暂时用尽，因此使用 0。
            int y = (q != null) ? q.val : 0;

            // 计算两个数字与前一个位置进位的和。
            int sum = carry + x + y;

            // 更新下一个位置的进位。sum / 10 得到进位。
            carry = sum / 10;
            // 新节点的数字是 sum % 10。
            int digit = sum % 10;

            // 使用计算出的数字创建一个新节点，并将其附加到结果列表中。
            current.next = new ListNode(digit);
            // 将 'current' 指针移动到这个新节点。
            current = current.next;

            // 如果 p 不为 null，则移动到 l1 中的下一个节点。
            if (p != null) {
                p = p.next;
            }
            // 如果 q 不为 null，则移动到 l2 中的下一个节点。
            if (q != null) {
                q = q.next;
            }
        }
        // 结果列表从 dummyHead 之后的节点开始。
        return dummyHead.next;
    }
}
```

### 核心数据结构
单向链表：每个节点存储一个整数的单个数字。数字按逆序存储（最低有效数字在头部）。结果和也以相同格式的单向链表形式返回。

### 主要算法思想
小学加法：该算法模拟手动、逐列相加两个数字的过程，并适当处理进位。迭代遍历：两个输入链表都从头到尾遍历。新节点迭代地添加到结果链表中。

### 相关问题
*   字符串相乘 (LeetCode #43)
*   二进制求和 (LeetCode #67)
*   两数相加 II (LeetCode #445)
*   数组形式的整数加法 (LeetCode #989)
*   加一链表 (LeetCode #369)
```
