---
title: 算法（六） —— Heap／Priority Queue
summary: Heap／Priority Queue
date: 2024-09-21

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


## Heap／Priority Queue


### 973. K Closest Points to Origin
[题目](https://leetcode.com/problems/k-closest-points-to-origin/description/)：给定一个坐标系，给出离原点最近的点。
<br>
Example 1:<br>
Input: points = [[3,3],[5,-1],[-2,4]], k = 2<br>
Output: [[3,3],[-2,4]]<br>
Explanation: The answer [[-2,4],[3,3]] would also be accepted.
```java
//最简单的Java排序法。
public int[][] kClosest(int[][] points, int k) {
        int[][] res = new int[k][2];
        Arrays.sort(points, (a,b)->((a[0]*a[0]+a[1]*a[1]) - (b[0]*b[0]+b[1]*b[1])));
        for(int i=0;i<k;i++){
            res[i][0] = points[i][0];
            res[i][1] = points[i][1];
        }
        return res;
    }
```
```java
//PriorityQueue (出给出逻辑，其余代码略)
public class Point implements Comparable<Point> {
    public int[][] kClosest(int[][] points, int k) {
        PriorityQueue<Point> q = new PriorityQueue<>();
        for (int[] i: points) {
            Point newP = new Point(i[0], i[1]);
            if(q.size() < k) q.offer(newP);
            else {
                Point lowP = q.peek();
                if (distance(lowP) > distance(newP)) {
                    q.poll();
                    q.offer(newP);
                }
            }
        }
        int[][] ans = new int[q.size()][2];
        int i = 0;
        while(!q.isEmpty()) {
            Point p = q.poll();
            ans[i] = new int[]{p.x, p.y};
            i++;
        }
        return ans;
    }
```
