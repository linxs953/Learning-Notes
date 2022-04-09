> 输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点



### 思路

- 计算链表长度
- 计算走到第k个节点需要执行多少次


```golang
func f(root *ListNode,k int) *ListNode{
    p := root
    length := 0
    for root.next != nil {
        root = root.next
        length++
    }
    for i := 0; i <= length - k:k++ {
        p = p.next
    }
    return p
}
```