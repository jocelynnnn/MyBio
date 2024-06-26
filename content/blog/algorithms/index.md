---
title: 算法篇
summary: Take full control of your personal brand and privacy by migrating away from the big tech platforms!
date: 2024-06-26

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com)'

authors:
  - jocelyn

tags:
  - Algorithm
---

|  算法   | 题号  |
|  ----  | ----  |
| 排序  | [148. Sort List：链表生序排列 / 链表归并排序](#148-sort-list) <br> [56. Merge Intervals：合并重叠区域](#56-merge-intervals-medium) <br>  [27. Remove elements](#27-remove-element)<br> [179. Largest Number](#179-largest-number)<br> [75. Sort Colors](#75-sort-colors)<br> [215. Kth Largest Element](#215-kth-largest-element) <br> [4. Median of Two Sorted Arrays](#4-median-of-two-sorted-arrays) |
| 链表  |  [206. Reverse Linked List](#206-reverse-linked-list)<br> [876. Middle of the Linked List](#876-middle-of-the-linked-list) <br> [160. Intersection of Two Linked Lists](#160-intersection-of-two-linked-lists)<br> [141. Linked List Cycle](#141-linked-list-cycle) ([142. Linked List Cycle II](#142-linked-list-cycle-ii))<br> [92. Reverse Linked List II](#92-reverse-linked-list-ii)<br> [328. Odd Even Linked List](#328-odd-even-linked-list)|
| Queue  |  [225. Implement Stack using Queues](#leetcode-225-implement-stack-using-queues)<br>[346. Moving Average from Data Stream](#leetcode-346-moving-average-from-data-stream) <br>[281. Zigzag Iterator](#leetcode-281-zigzag-iterator) <br>[1429. First Unique Number](#leetcode-1429-first-unique-number)<br>[54. Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)|
| Stack  | [155. Min Stack](#155-min-stack) <br> [232. Implement Queue using Stacks](#leetcode-232-implement-queue-using-stacks)<br> [150. Evaluate Reverse Polish Notation](#150-evaluate-reverse-polish-notation)<br>[224. Basic Calculator II (I, II, III, IV)](#224-basic-calculator)<br> [20. Valid Parentheses](#20-valid-parentheses)<br> [1472. Design Browser History](#1472-design-browser-history)<br> [1209. Remove All Adjacent Duplicates in String II](#1209-remove-all-adjacent-duplicates-in-string-ii)<br> 1249. Minimum Remove to Make Valid Parentheses<br> 735. Asteroid Collision|

{{< toc mobile_only=true is_open=true >}}


## 排序

### [148. Sort List (Medium)](https://leetcode.com/problems/sort-list/description/)

链表升序排序

Given the head of a linked list, return the list after sorting it in ascending order.

```java
//方法一：转化成ArrayList排序
public ListNode sortList(ListNode head) {
        ListNode resList = new ListNode();
        ArrayList<Integer> tmpArr = new ArrayList<>();
        while (head != null){
            tmpArr.add(head.val);
            head = head.next;
        }
        tmpArr.sort((x,y)->Integer.compare(y,x));
        for (Integer a: tmpArr){
            ListNode tmp = new ListNode();
            tmp.val = a;
            tmp.next = resList.next;
            resList.next = tmp;
        }
        return resList.next;
    }
```

```java
//方法二：转化为mergeSort，递归解决
public ListNode sortList(ListNode head) {
        if (head == null || head.next ==null){
            return head;
        }
        ListNode p1 = head, p2 = head;
        ListNode pre = null;
        while (p2 != null && p2.next != null){
            pre = p1;
            p1 = p1.next;
            p2 = p2.next.next;
        }
        pre.next = null;
        ListNode l1 = sortList(head);
        ListNode l2 = sortList(p1);
        return mergeTwoList(l1,l2);
    }

    public ListNode mergeTwoList(ListNode head1, ListNode head2){
        ListNode dummy = new ListNode();
        ListNode p = dummy;
        while(head1 != null && head2 != null){
            if (head1.val <= head2.val){
                p.next = head1;
                head1 = head1.next;
            }else{
                p.next = head2;
                head2 = head2.next;
            }
            p = p.next;
        }
        if (head1 == null){
            p.next = head2;
        }else{
            p.next = head1;
        }
        return dummy.next;
    }
```

### [56. Merge Intervals (Medium)](https://leetcode.com/problems/merge-intervals/description/)

很经典的面试题，合并重叠区间

**Example 1:**

Input: intervals = [[1,3],[2,6],[8,10],[15,18]]

Output: [[1,6],[8,10],[15,18]]

Explanation: Since intervals [1,3] and [2,6] overlap, merge them into [1,6].

```java
public int[][] merge(int[][] intervals) {
    Arrays.sort(intervals, new Comparator <int[]>(){
            @Override
            public int compare(int[] o1, int[] o2){
                if (o1[0] == o2[0]){
                    return o1[1]-o2[1];
                }
                return o1[0]-o2[0];
            }
        });

    int len = intervals.length;
    int max = intervals[0][1];
    int indexi = intervals[0][0], indexj=-1;

    List<int[]> res = new ArrayList<>();
    for (int i=1; i<len; i++){
        if (intervals[i][0] > max){
            indexj = max;
            res.add(new int[]{indexi,indexj});
            indexi = intervals[i][0];
            indexj=-1;
            max = intervals[i][1];
        }else {
            max = Math.max(max,intervals[i][1]);
        }
    }
    if (indexj == -1){
        res.add(new int[]{indexi,max});
    }
    return res.toArray(new int[res.size()][]);
}
```

```java
//简洁的答案
public int[][] merge(int[][] intervals) {
    //用Arrays.sort对数组排序的lambda写法
    Arrays.sort(intervals, (x,y) -> x[0]-y[0]);
    List<int[]> res = new ArrayList<>();
    res.add(intervals[0]);
    for (int i=1; i<intervals.length; i++){
        int[] cur = new int[2];
        if (intervals[i][0] <= res.get(res.size()-1)[1]){
            cur[0] = Math.min(res.get(res.size()-1)[0],intervals[i][0]);
            cur[1] = Math.max(res.get(res.size()-1)[1],intervals[i][1]);
            res.set(res.size()-1, cur);
        }else{
            res.add(intervals[i]);
        }
    }
    return res.toArray(new int[res.size()][]);
}
```

### [27. Remove Element](https://leetcode.com/problems/remove-element/description/)

移除数组中的指定大小为val的元素，并返回其余元素数量。

**分析**：这道题的答案判定方法比较简单，返回k为非val的元素数量，并检查数组的前k个元素。所以k之后的元素不用处理。

```java
//如果判定方式改变，可能就不能使用这个方法了
public int removeElement(int[] nums, int val) {
        int index = 0;
        for (int i=0; i<nums.length; i++){
            if (nums[i] != val){
                nums[index] = nums[i];
                index ++;
            }
        }
        return index;
    }
```

### [179. Largest Number](https://leetcode.com/problems/largest-number/description/)

给定一串非负整数，以字符串输出能拼接出的最大数。

**Example:**

Input: nums = [3,30,34,5,9]

Output: "9534330"。

**分析：** 思路很简单就是按照字典序从大到小对数字排序。比较容易想到的是一位位比较，但是如：3和30，谁大呢？对于位数不同的数字，需要循环比较，例如：34和343056。34->343434,那么它自然比343056大。<br>这里其实还有一种做法，就是直接将数字转化成字符串并拼接，利用string1.compareTo(string2)来比较即可。

```java
public String largestNumber(int[] nums) {
        Integer[] input = new Integer[nums.length];
        for (int i=0; i<nums.length; i++){
            input[i] = nums[i];
        }
        //重写compare实现Comparator
        Arrays.sort(input, new Comparator<Integer>(){
            @Override
            public int compare(Integer a, Integer b){
                String as = a.toString();
                String bs = b.toString();
                String t1 = as +""+bs;
                String t2 = bs +""+as;
                return t2.compareTo(t1) ;
            }
        });
        //字符串拼接的场景下，应使用StringBuilder
        StringBuilder sb = new StringBuilder();
        for (int i=0; i<input.length; i++){
            sb.append(input[i]);
        }
        String res = sb.toString();
        int index = 0;
        while(index<res.length() && res.charAt(index) == '0'){
            index ++;
        }
        return res.substring(index).equals("")? "0":res.substring(index);
    }
```

### [75. Sort Colors](https://leetcode.com/problems/sort-colors/description/)

对给定数组进行排序，数组中只有0，1，2三种数字分别对应红白蓝三种颜色。

**Example:**

Input: nums = [2,0,2,1,1,0]

Output: [0,0,1,1,2,2]

**分析：** 三路分区算法（3-way partitioning）。需要low，mid，high三个指针。[0,low）之间为第一路，[low,high]之间为第二路,(high,n-1]为第三路。该算法背后的想法是将所有0放在低指针之前，将所有2放在高指针之后，并将所有1放在低指针和高指针之间。该算法通过数组移动中间指针，将每个位置的值与1进行比较。如果值为0，则元素与低指针的元素交换，低指针和中指针递增。如果值为2，则元素与高指针处的元素交换，高指针被递减。如果值为1，则中间指针只是递增。

```java
public void sortColors(int[] nums) {
        int low=0, mid=0, high = nums.length-1;
        while(mid <= high){
            if (nums[mid] == 2){
                swap(nums, mid, high);
                high--;
            }else if(nums[mid]==0){
                swap(nums, mid, low);
                low++;
                mid++;
            }else{
                mid++;
            }
        }
    }
    public void swap(int[] nums, int a, int b){
        int temp = nums[a];
        nums[a] = nums[b];
        nums[b] = temp;
    }
```

### [215. Kth Largest Element](https://leetcode.com/problems/kth-largest-element-in-an-array/)

寻找给定数组中的第K大数字。要求不对数组进行完全排序。

**解法一：基于快排的思路，partition**

快排的核心思路是选择一个哨兵，每轮交换后使得哨兵左边的数都大于（或小于）它，右边的数都小于（或大于）它，当哨兵的index==k（或length-k）时，哨兵即为第K大的数。以下为具体代码：

```java
public int findKthLargest(int[] nums, int k) {
        int left = 0, right = nums.length-1;
        int curr = partition(nums,left,right);
        while (curr+1 != k){
            if (curr+1 > k){
                right = curr-1;
                curr = partition(nums,left,right);
            }else{
                left = curr +1;
                curr = partition(nums,left,right);
            }
        }
        return nums[curr];
    }

    public int partition(int[] nums, int left, int right){
        int i=left, j=right;
        int pivot = nums[i];
        while(i < j){
            //这里切记循环的顺序不能变！如果哨兵初始位置是i，那么一定要先扫j
            while(i < j && nums[j] <= pivot){
                j--;
            }
            while(i < j && nums[i] >= pivot){
                i++;
            }
            int tmp = nums[i];
            nums[i] = nums[j];
            nums[j] = tmp;
        }
        //如果循环的顺序改变，那么哨兵将无法回到正确的位置
        nums[left] = nums[i];
        nums[i] = pivot;
        return i;
    }
```

**解法一：基于堆排序的思路，建立一个大顶堆，然后依次弹出至第K个数**

直接用优先队列实现一个堆。以下为具体代码：

```java
public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> queue = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2-o1;
            }
        });
        for (int item: nums){
            queue.add(item);
        }
        for (int i=0; i<k-1; i++){
            queue.poll();
        }

        return queue.peek();
    }
```

### [4. Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)

对两个有序数组，找中位数。

**思路：开辟新的大小为m+n的数组，将两个有序数组依次放入，之后根据m+n的奇偶性取中位数**

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int m=nums1.length, n=nums2.length;
        int[] nums = new int[m+n];
        int i=0,j=0,p=0;

        while(i<m && j<n){
            if (nums1[i] <= nums2[j]){
                nums[p] = nums1[i];
                i++;
                p++;
            }else{
                nums[p] = nums2[j];
                j++;
                p++;
            }
        }
        while(i<m){
            nums[p] = nums1[i];
            i++;
            p++;
        }
        while(j<n){
            nums[p] = nums2[j];
            j++;
            p++;
        }
        if((m+n)%2 != 0){
            return (float)nums[(m+n)/2];
        }
        return (float)(nums[(m+n)/2-1] + nums[(m+n)/2])/2;
    }
```

**优化：其实新数组不是必须的，我们只需要记录中位的两个数字即可。另外，循环可以提前终止，扫到(m+n)/2+1后即可结束。代码如下：**

```java
public double findMedianSortedArrays2(int[] nums1, int[] nums2) {
        int i=0,j=0,index=0;
        int m=nums1.length, n=nums2.length;
        int a1=-1,a2=-1;
        while (i<m && j<n){
            if (index > (m+n)/2){
                break;
            }
            a1 = a2;
            if (nums1[i] <= nums2[j]){
                a2=nums1[i];
                i++;
                index++;
            }else{
                a2=nums2[j];
                j++;
                index++;
            }
        }
        if (i>=m){
            while (index<=(m+n)/2){
                a1 = a2;
                a2 = nums2[j];
                j++;
                index++;
            }
        }
        else if (j>=n){
            while (index<=(m+n)/2){
                a1 = a2;
                a2 = nums1[i];
                i++;
                index++;
            }
        }
        if ((m+n)%2 != 0){
            return (float)a2;
        }else{
            return (float)(a1+a2)/2.0;
        }
    }
```

### [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/description/)

链表转置：新建dummy，头插法依次插入链表元素即可。

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

### [876. Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/description/)

寻找链表的中间节点，如中间节点有两个，则返回第二个。

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

### [160. Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/description/)

寻找A和B两个链表的交叉点，如没有则返回null。

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

### [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/description/)

判断链表是否有环。

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

### [142. Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/description/)

判断链表是否有环，如有，返回环起始点。

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

### [92. Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/description/)

转置链表中的指定子段。

**解法：和链表转置的基本思路一致，新设dummy节点，将指定子段用头插法进行转置，另外再记录子段的前后节点即可。**

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

### [328. Odd Even Linked List](https://leetcode.com/problems/odd-even-linked-list/description/)

链表按index奇偶顺序排列head = [2,1,3,5,6,4,7]->[2,3,6,7,1,5,4]

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

### [Leetcode 225. Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/)

用Queue实现一个栈。

**分析：以下用链表结构实现。**

```java
class MyStack {

    private Queue<Integer> q;
    public MyStack() {
        q = new LinkedList<>();
    }

    public void push(int x) {
        q.add(x);
        for (int i=1; i<q.size(); i++){
            q.add(q.remove());
        }
    }

    public int pop() {
        return q.remove();
    }

    public int top() {
        return q.peek();
    }

    public boolean empty() {
        return q.isEmpty();
    }
}
```

### [Leetcode 346. Moving Average from Data Stream](https://leetcode.com/problems/moving-average-from-data-stream/description/)

计算滑动窗口的平均值，

案例：

```
MovingAverage m = new MovingAverage(3);
m.next(1) = 1
m.next(10) = (1 + 10) / 2
m.next(3) = (1 + 10 + 3) / 3
m.next(5) = (10 + 3 + 5) / 3
```

```java
public class MovingAverage {
    private Queue<Integer> q = new LinkedList<>();
    private int size;
    private long sum=0;

    public MovingAverage(int size) {
        this.size = size;
    }

    public double next(int val) {
        if (q.size() == size){
            sum -= q.poll();
            q.add(val);
            sum += val;
        }else{
            q.add(val);
            sum += val;
        }
        return (double)sum/q.size();
    }
}
```

### Leetcode 281. Zigzag Iterator

给定两个向量，交替输出元素。

```
Example 1:
Input: v1 = [1,2], v2 = [3,4,5,6]
Output: [1,3,2,4,5,6]
Explanation: By calling next repeatedly until hasNext returns false, the order of elements returned by next should be: [1,3,2,4,5,6].

Example 2:
Input: v1 = [1], v2 = []
Output: [1]
```

**分析：用Queue存储向量的迭代器，每次输出元素时，首先取出迭代器，及其下一个元素，再将迭代器放回队尾。**

```java
public class ZigzagIterator {
    private Queue<Iterator> q;

    public ZigzagIterator(List<Integer> v1, List<Integer> v2) {
        q = new LinkedList<>();
        if (!v1.isEmpty()){
            q.offer(v1.iterator());
        }
        if (!v2.isEmpty()){
            q.offer(v1.iterator());
        }
    }

    public int next() {
        Iterator<Integer> iterator = q.poll();
        int item =  iterator.next();
        if (iterator.hasNext()){
            q.offer(iterator);
        }
        return item;
    }

    public boolean hasNext() {
        return !q.isEmpty();
    }
}
```

### [54. Spiral Matrix](https://leetcode.com/problems/spiral-matrix/description/)

螺旋打印二维向量:

Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]

Output: [1,2,3,6,9,8,7,4,5]

**解法：定义上下左右四个边界，并分为四个方向分别处理。**

```java
public List<Integer> spiralOrder(int[][] matrix) {
        int i=0,j=0;
        int n = matrix.length, m = matrix[0].length;
        int left = 0, right = m;
        int up = 0, down = n;
        List<Integer> res = new ArrayList<>();
        char direct = 'r';//l (left) r (right) u(up) d(down)

        while (left < right && up < down){
            if (direct == 'r'){
                for (j = left; j < right; j++){
                    res.add(matrix[i][j]);
                }
                j--;
                up++;
                direct = 'd';
            }
            else if (direct == 'd'){
                for (i=up; i < down; i++){
                    res.add(matrix[i][j]);
                }
                i--;
                right--;
                direct = 'l';
            }
            else if (direct == 'l'){
                for (j=right-1; j>=left; j--){
                    res.add(matrix[i][j]);
                }
                j++;
                down --;
                direct = 'u';
            }
            else if (direct =='u'){
                for (i=down-1; i>=up; i--){
                    res.add(matrix[i][j]);
                }
                i++;
                left ++;
                direct = 'r';
            }
        }
        return res;
    }
```

### Leetcode 1429. First Unique Number

给定一个数组，实现以下操作：

初始化队列，并包含队列中的初始元素。

showFirstUnique(): 返回队列中第一个唯一数字的值，如果不存在则返回 -1。

add(value): 将数字 value 添加到队列的末尾。

**解法：用HashMap存放每个数字出现的次数，用queue放所有数。寻找唯一数时，从队尾依次查，如果该数出现次数>1，那么直接从队列中弹出（HashMap中不变），直到找到出现次数=1的数为止，否则返回-1.**

```java
public class FirstUnique {
    Map<Integer, Integer> numCount;
    Queue<Integer> queue;
    public FirstUnique(int[] nums) {
        numCount = new HashMap<>();
        queue = new LinkedList<>();
        for (int item: nums){
            add(item);
        }
    }
    public int showFirstUnique() {
        while (!queue.isEmpty()){
            if (numCount.get(queue.peek()) > 1){
                queue.poll();
            }
            else{
                return queue.peek();
            }
        }
        return -1;
    }
    public void add(int value) {
        if (numCount.containsKey(value)){
            numCount.put(value,numCount.get(value) +1);
        }else{
            numCount.put(value,1);
            queue.offer(value);
        }
    }
}
```

### [155. Min Stack](https://leetcode.com/problems/min-stack/description/)

设计一个堆，并实现以下方法，要求每个方法的时间复杂度都在O(1)。

**解法：用两个栈，stack1依次存所有元素，stack2依次存最小值。**

```java
class MinStack {
    private Stack<Integer> stack1;
    private Stack<Integer> stack2;
    public MinStack() {
        stack1 = new Stack<>();
        stack2 = new Stack<>();
    }

    public void push(int val) {
        stack1.push(val);
        if (stack2.isEmpty() || stack2.peek()>=val){
            stack2.push(val);
        }
    }

    public void pop() {
        int val = stack1.pop();
        if (val == stack2.peek()){
            stack2.pop();
        }
        return;
    }

    public int top() {
        return stack1.peek();
    }

    public int getMin() {
        return stack2.peek();
    }
}
```

### [Leetcode 232. Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/description/)

用两个栈实现一个队列。

```java
class MyQueue {

    Stack<Integer> stack1;
    Stack<Integer> stack2;
    public MyQueue() {
        stack1 = new Stack<>();
        stack2 = new Stack<>();
    }

    public void push(int x) {
        while(!stack1.isEmpty()){
            stack2.push(stack1.pop());
        }
        stack1.push(x);
        while(!stack2.isEmpty()){
            stack1.push(stack2.pop());
        }
    }

    public int pop() {
        return stack1.pop();
    }

    public int peek() {
        return stack1.peek();
    }

    public boolean empty() {
        return stack1.isEmpty();
    }
}
```

### [150. Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/description/)

逆波兰表示法计算

```
Example 1:
Input: tokens = ["2","1","+","3","*"]
Output: 9
Explanation: ((2 + 1) * 3) = 9

Example 2:
Input: tokens = ["4","13","5","/","+"]
Output: 6
Explanation: (4 + (13 / 5)) = 6
```

```java
public int evalRPN(String[] tokens) {
        Stack<Integer> nums = new Stack<>();
        //Stack<char> opr = new Stack<>();
        for (int i=0; i<tokens.length; i++){
            if (tokens[i].equals("+")){
                int temp1 = nums.pop();
                int temp2 = nums.pop();
                int tmp = temp1 + temp2;
                nums.push(tmp);
            }else if(tokens[i].equals("-")){
                int temp2 = nums.pop();
                int temp1 = nums.pop();
                int tmp = temp1 - temp2;
                nums.push(tmp);
            }else if(tokens[i].equals("*")){
                int temp1 = nums.pop();
                int temp2 = nums.pop();
                int tmp = temp1 * temp2;
                nums.push(tmp);
            }else if(tokens[i].equals("/")){
                int temp2 = nums.pop();
                int temp1 = nums.pop();
                int tmp = temp1 / temp2;
                nums.push(tmp);
            }else{
                nums.push(Integer.parseInt(tokens[i]));
            }
        }
        return nums.pop();
    }
```

### [224. Basic Calculator](https://leetcode.com/problems/basic-calculator/description/)

实现一个基础的加减法计算器。

**解法：** 由于这道题只有加减法，这道题的计算只需要使用一个栈，记录当前数字和符号即可。而计算的方式可以简化成两步：

第一步：result \*= sign，

第二步：result += number。

**按'+', '-', '(', ')'和数字五种情况分析：**

**1. 数字：** 输入的数字并不一定是个位数，所以如果当前是数字，需要与前序数字整合，number起初为0：number = 10 * number + (int)(c - '0');

**2.+ ：** result += sign * number; number = 0; sign = 1;

**3.- ：** result += sign * number; number = 0; sign = -1;

**4.( :** 前序答案要入栈，等待括号里的计算完成后，再行计算。

**5.) :** 当读到右括号时，括号内的计算已经完成了，并记录在当前的number和sign中，此时，只要把当前括号中的计算结果与栈内结果合并计算，那么此时需要从栈中弹出前一个符号及数字，并计算，记录在result中。

代码如下：

```java
public int calculate(String s) {
        Stack<Integer> stack = new Stack<Integer>();
        int result = 0;
        int number = 0;
        int sign = 1;
        for(int i = 0; i < s.length(); i++){
            char c = s.charAt(i);
            if(Character.isDigit(c)){
                number = 10 * number + (int)(c - '0');
            }else if(c == '+'){
                result += sign * number;
                number = 0;
                sign = 1;
            }else if(c == '-'){
                result += sign * number;
                number = 0;
                sign = -1;
            }else if(c == '('){
                //we push the result first, then sign;
                stack.push(result);
                stack.push(sign);
                //reset the sign and result for the value in the parenthesis
                sign = 1;
                result = 0;
            }else if(c == ')'){
                result += sign * number;
                number = 0;
                result *= stack.pop();    //stack.pop() is the sign before the parenthesis
                result += stack.pop();   //stack.pop() now is the result calculated before the parenthesis

            }
        }
        if(number != 0) result += sign * number;
        return result;
    }
```

### [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)

给定String，包含 '(', ')', '{', '}', '[' and ']'，检查括号是否匹配。

**解法：Stack简单题。**

```java
public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        char[] input = s.toCharArray();
        for (int i=0; i<input.length; i++){
            if (input[i]=='('
                    || input[i]=='['
                    || input[i]=='{'){
                stack.push(input[i]);
                continue;
            }else if(input[i]==')'){
                if (!stack.isEmpty() && stack.peek() == '('){
                    stack.pop();
                    continue;
                }
            }else if(input[i]==']'){
                if (!stack.isEmpty() && stack.peek() == '['){
                    stack.pop();
                    continue;
                }
            }else if(input[i]=='}'){
                if (!stack.isEmpty() && stack.peek() == '{'){
                    stack.pop();
                    continue;
                }
            }
            return false;
        }
        return stack.isEmpty();
    }
```

### [1472. Design Browser History](https://leetcode.com/problems/design-browser-history/description/)

实现浏览器的前进后退功能。

**解法： 使用两个栈解决。**

```java
class BrowserHistory {
    Stack<String> stack1 = new Stack<>();
    Stack<String> stack2 = new Stack<>();

    public BrowserHistory(String homepage) {
        stack1.add(homepage);
    }

    public void visit(String url) {
        stack1.add(url);
        stack2 = new Stack<>();
    }

    public String back(int steps) {
        while (stack1.size()>1 && steps > 0){
            stack2.add(stack1.pop());
            steps--;
        }
        return stack1.peek();
    }

    public String forward(int steps) {
        while (stack2.size()>0 && steps > 0){
            stack1.add(stack2.pop());
            steps--;
        }
        return stack1.peek();
    }
}

/**
 * Your BrowserHistory object will be instantiated and called as such:
 * BrowserHistory obj = new BrowserHistory(homepage);
 * obj.visit(url);
 * String param_2 = obj.back(steps);
 * String param_3 = obj.forward(steps);
 */
```

### [1209. Remove All Adjacent Duplicates in String II](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string-ii/description/)

给定一个String s和一个整型k，去掉s中连续出现k次的字符，并输出最终结果。

**思路：为了不重复扫描，一定要记录每个字符连续出现的次数。这里使用一个栈来记录每个不同字符出现的次数，用StringBuilder拼接字符串。**

```java
public String removeDuplicates(String s, int k) {
        if (s.length() < k){
            return s;
        }
        Stack<Integer> countStack = new Stack<>();
        StringBuilder sb = new StringBuilder();
        for (int i=0; i<s.length(); i++){
            sb.append(s.charAt(i));
            int lastIndex = sb.length()-1;
            if (lastIndex>0 &&
            sb.charAt(lastIndex-1) == sb.charAt(lastIndex)){
                int count = countStack.pop();
                if (count + 1 < k){
                    countStack.push(count + 1);
                }
                else {
                    sb.setLength(sb.length() - k);
                }
            }else {
                countStack.push(1);
            }
        }
        return sb.toString();
    }
```
