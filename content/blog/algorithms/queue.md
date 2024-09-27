---
title: 算法（三） —— Queue
summary: Queue
date: 2023-12-25

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


## Queue


### Leetcode 225. Implement Stack using Queues

[题目](https://leetcode.com/problems/implement-stack-using-queues/)：用Queue实现一个栈。<br> **分析：以下用链表结构实现。**
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
### Leetcode 346. Moving Average from Data Stream
计算滑动窗口的平均值，<br>案例：<br>MovingAverage m = new MovingAverage(3);<br>
m.next(1) = 1<br>
m.next(10) = (1 + 10) / 2<br>
m.next(3) = (1 + 10 + 3) / 3<br>
m.next(5) = (10 + 3 + 5) / 3<br>
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

Example 1:<br>
Input: v1 = [1,2], v2 = [3,4,5,6]<br>
Output: [1,3,2,4,5,6]<br>
Explanation: By calling next repeatedly until hasNext returns false, the order of elements returned by next should be: [1,3,2,4,5,6].

Example 2:<br>
Input: v1 = [1], v2 = []<br>
Output: [1]<br>

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

### 54. Spiral Matrix
[题目](https://leetcode.com/problems/spiral-matrix/description/)：螺旋打印二维向量:<br>
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]<br>
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
<br>初始化队列，并包含队列中的初始元素。<br>
showFirstUnique(): 返回队列中第一个唯一数字的值，如果不存在则返回 -1。
<br>add(value): 将数字 value 添加到队列的末尾。

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