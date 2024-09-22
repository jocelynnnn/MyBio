---
title: 算法（一） —— 排序
summary: Sort
date: 2023-01-25

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


## Sort

### 148. Sort List (Medium)

[题目](https://leetcode.com/problems/sort-list/description/)：链表升序排序

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

### 56. Merge Intervals (Medium)

[题目](https://leetcode.com/problems/merge-intervals/description/)：很经典的面试题，合并重叠区间

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

### 27. Remove Element
[题目](https://leetcode.com/problems/remove-element/description/)：移除数组中的指定大小为val的元素，并返回其余元素数量。

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

### 179. Largest Number

[题目](https://leetcode.com/problems/largest-number/description/)：给定一串非负整数，以字符串输出能拼接出的最大数。

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

### 75. Sort Colors

[题目](https://leetcode.com/problems/sort-colors/description/)：对给定数组进行排序，数组中只有0，1，2三种数字分别对应红白蓝三种颜色。

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

### 215. Kth Largest Element

[题目](https://leetcode.com/problems/kth-largest-element-in-an-array/)：寻找给定数组中的第K大数字。要求不对数组进行完全排序。

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

### 4. Median of Two Sorted Arrays
[题目](https://leetcode.com/problems/median-of-two-sorted-arrays/)
：对两个有序数组，找中位数。

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

---
title: 算法（一） —— 排序
summary: Sort
date: 2024-01-25

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


## Sort

### 148. Sort List (Medium)

[题目](https://leetcode.com/problems/sort-list/description/)：链表升序排序

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

### 56. Merge Intervals (Medium)

[题目](https://leetcode.com/problems/merge-intervals/description/)：很经典的面试题，合并重叠区间

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

### 27. Remove Element
[题目](https://leetcode.com/problems/remove-element/description/)
：移除数组中的指定大小为val的元素，并返回其余元素数量。

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

### 179. Largest Number

[题目](https://leetcode.com/problems/largest-number/description/)：给定一串非负整数，以字符串输出能拼接出的最大数。

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

### 75. Sort Colors

[题目](https://leetcode.com/problems/sort-colors/description/)：对给定数组进行排序，数组中只有0，1，2三种数字分别对应红白蓝三种颜色。

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

### 215. Kth Largest Element

[题目](https://leetcode.com/problems/kth-largest-element-in-an-array/)：寻找给定数组中的第K大数字。要求不对数组进行完全排序。

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

### 4. Median of Two Sorted Arrays
[题目](https://leetcode.com/problems/median-of-two-sorted-arrays/)
：对两个有序数组，找中位数。

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
