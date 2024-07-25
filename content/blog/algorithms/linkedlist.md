---
title: 算法（二） —— 链表
summary: 
date: 2024-07-25

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'title-sort.jpg'

authors:
  - jocelyn

tags:
  - Algorithm
---


{{< toc mobile_only=true is_open=true >}}


## LinkedList

### 206. Reverse Linked List
[题目](https://leetcode.com/problems/reverse-linked-list/description/)：链表转置：新建dummy，头插法依次插入链表元素即可。
```java
public ListNode reverseList(ListNode head) {
        ListNode dummy = new ListNode();
        ListNode next = new ListNode();
        while(head != null){
            next = head.next;
            head.next = dummy.next;
            dummy.next = head;
            head = next;
        }
        return dummy.next;
    }
```

### 876. Middle of the Linked List
[题目](https://leetcode.com/problems/middle-of-the-linked-list/description/)：寻找链表的中间节点，如中间节点有两个，则返回第二个。

**解法：** 双指针，一个走一步，一个走两步。
```java
public ListNode middleNode(ListNode head) {
        ListNode p1=head, p2=head;
        while(p2!=null && p2.next != null){
            p2 = p2.next.next;
            p1 = p1.next;
        }
        return p1;
    }
```
### 160. Intersection of Two Linked Lists
[题目](https://leetcode.com/problems/intersection-of-two-linked-lists/description/)：寻找A和B两个链表的交叉点，如没有则返回null。

**分析：** 两个指针分别遍历A和B，至null后再遍历B和A。当两个指针指向同一个节点时，即为交叉点。否则，两个指针将同时指向null。
```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode a = headA;
        ListNode b = headB;
        //结束条件为，两指针在交叉点相遇，或两指针都指向null即为无交叉点。
        while(a != b){
            a = a==null? headB : a.next;
            b = b==null? headA : b.next;
        }
        return a;
    }
```

### 141. Linked List Cycle
[题目](https://leetcode.com/problems/linked-list-cycle/description/)：判断链表是否有环。<br>
**分析：双指针，一个走一步，一个走两步，如果有环则指针必相遇。**
```java
public boolean hasCycle(ListNode head) {
        ListNode p1 = head, p2 = head;
        while (p2!=null && p2.next != null){
            p1 = p1.next;
            p2 = p2.next.next;
            if (p1 == p2){
                return true;
            }
        }
        return false;
    }
```
### 142. Linked List Cycle II
[题目](https://leetcode.com/problems/linked-list-cycle-ii/description/)：判断链表是否有环，如有，返回环起始点。<br>
**解法一：暴力法，用Set记录每一个点，发现重复点时即为环起点。**
```java
public ListNode detectCycle(ListNode head) {
        Set<ListNode> nodes = new HashSet<>();
        nodes.add(head);
        ListNode p1 = head;
        while (true){
            if (p1 == null || p1.next == null){
                return null;
            }
            p1 = p1.next;
            if(nodes.contains(p1)){
                return p1;
            }
            nodes.add(p1);
        }
    }
```
**解法二：双指针，一个走一步，一个走两步，如果有环则指针必相遇。相遇后，将慢指针重新指向head，快指针恢复为慢指针。再次相遇时即为环头。**
```java
public ListNode detectCycle(ListNode head) {
        ListNode p1 = head, p2 = head;
        while (p2!=null && p2.next != null){
            p1 = p1.next;
            p2 = p2.next.next;
            if (p1 == p2){
                p1 = head;
                while (p1!=p2){
                    p1 = p1.next;
                    p2 = p2.next;
                }
                return p1;
            }
        }
        return null;
    }
```
### 92. Reverse Linked List II

[题目](https://leetcode.com/problems/reverse-linked-list-ii/description/)：转置链表中的指定子段。<br>**解法：和链表转置的基本思路一致，新设dummy节点，将指定子段用头插法进行转置，另外再记录子段的前后节点即可。**
```java
public ListNode reverseBetween(ListNode head, int left, int right) {
        //在head前增加一个空节点pre
        ListNode pre = new ListNode();
        pre.next = head;
        ListNode cur = head;
        //把head往前挪一格
        head = pre;
        for (int i=1; i<left; i++){
            pre = pre.next;
            cur = cur.next;
        }
        ListNode dummy = new ListNode();
        //第一个节点将成为转置后的尾节点，用来拼接后续节点，需要先记录
        ListNode tail = cur;
        for (int i=left; i<=right; i++){
            pre.next = cur.next;
            cur.next = dummy.next;
            dummy.next = cur;
            cur = pre.next;
        }
        pre.next = dummy.next;
        while(cur!=null){
            tail.next = cur;
            cur = cur.next;
            tail = tail.next;
        }
        return head.next;
    }
```

### 328. Odd Even Linked List
[题目](https://leetcode.com/problems/odd-even-linked-list/description/)：链表按index奇偶顺序排列head = [2,1,3,5,6,4,7]->[2,3,6,7,1,5,4]
```java
public ListNode oddEvenList(ListNode head) {
        ListNode dummy = new ListNode();
        ListNode dummy2 = new ListNode();
        ListNode p1 = dummy;
        ListNode p2 = dummy2;
        while (head!=null && head.next!=null){
            p1.next = head;
            p1 = p1.next;
            head = head.next;

            p2.next = head;
            p2 = p2.next;
            head = head.next;
        }
        p2.next = null;
        if (head!=null){
            p1.next = head;
            p1 = p1.next;
        }
        p1.next = dummy2.next;
        return dummy.next;
    }
```
