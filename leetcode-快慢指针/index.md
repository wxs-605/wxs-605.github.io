# LeetCode 快慢指针


小结一下今天刷的关于链表的题，设计的主要知识点是快慢指针，虽然有点简单，但还是总结一下吧。

**快慢指针思想**
- 常规： 定义一个slow慢指针，一个fast快指针，慢指针走一步时，快指针走两步。当快指针走到链表尾时，慢指针刚好位于链表中点。
- 进阶：快指针先走N步，慢指针不动，然后快指针走一步时慢指针也走一步。当快指针到达链表尾时，慢指针刚好位于倒数第N个位置。

## 环形链表
给你一个链表的头节点，判断链表中是否有环。

### 我的思路：
使用快慢指针的常规做法，当一个链表中有坏时，快指针和慢指针走了n步之后走，必定在环中相遇，因为快指针每次都比慢指针多走一步。若链表中没有环，那么快慢指针则不可能相遇。

- 时间复杂度： 快慢指针都走了n步，时间复杂度为O(N)；
- 空间复杂度： 使用了常数个临时变量，故空间复杂度为O(1)；


## 环形链表II
给你一个链表的头节点，判断其中是否有环，如果有环，则返回坏的起始节点，否则返回N空指针。

### 我的思路：
使用快慢指针的常规做法解决。设链表中非环部分的长度为a，两指针第一次相遇时慢指针在环里走过的长度为b，环里剩下的长度为c，那么两指针第一次相遇时，有以下的关系：

2 * (a + b) = a + (n + 1) * b + n * c;

化简一下有 => a = (n - 1)(b + c) + c；

由此可以知道，当快慢指针第一次相遇后，把快指针移到头节点，然后慢指针走一步，快指针也走一步，当他们走了a步之后再次相遇时，就是刚好位于链表中环的起始位置。

- 时间复杂度： 相当于遍历了两次链表，故时间复杂度为O(N)；
- 空间复杂度： 使用了常数个临时指针，故空间复杂度为O(1)；


## 删除链表中倒数第N个节点
给你一个链表，删除链表的倒数第 n个结点，并且返回链表的头结点。

### 我的思路一：
快慢指针的进阶用法，快指针先走n步，然后快慢指针再同时一步一步的走，当快指针到达链表尾时，慢指针刚好位于倒数第N个位置，此时对链表进行相应的操作即可。

- 时间复杂度： 遍历一次链表，故时间复杂度为O(N)；
- 空间复杂度： 使用了常数个临时变量，空间复杂度为O(1)；

### 我的思路二：
先遍历一次链表，统计出链表中的节点个数，在下一次遍历中找到倒数第n个节点，将其删除。

- 时间复杂度： 遍历两次链表，故时间复杂度为O(N)；
- 空间复杂度： 使用了常数个临时变量，空间复杂度为O(1)；
