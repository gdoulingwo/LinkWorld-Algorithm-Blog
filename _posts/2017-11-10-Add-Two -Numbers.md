---
title: Add Two Numbers-Notzuonotdied
date: 2017-10-10 23:37:31
---

You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
```

answer:

Kotlin
``` kotlin
class Solution {
    // ListNode? 后面的“？”表示该引用可以为空
    fun addTwoNumbers(l1: ListNode?, l2: ListNode?): ListNode? {
        // 首先，保存我们的头结点
        val head = ListNode(0)
        var p = l1
        var q = l2
        // 设置游标
        var curr: ListNode? = head
        // 是否进位
        var carry = 0
        // 循环直到两者都为null的时候停止
        while (p != null || q != null) {
            val x = p?.`val` ?: 0
            val y = q?.`val` ?: 0
            // 我们需要从这里拿到进位
            // 换句话说就是拿到个位以上的部分
            val sum = carry + x + y
            carry = sum / 10
            // 增加下一个节点
            curr!!.next = ListNode(sum % 10)
            curr = curr.next
            p = p?.next
            q = q?.next
        }
        // 停止的时候需要判断进位是否为0
        // 如果为0的话就要再进一位
        if (carry > 0) {
            curr!!.next = ListNode(carry)
        }
        return head.next
    }
}
```
