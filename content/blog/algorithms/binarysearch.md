---
title: 算法（七） —— Binary Search
summary: Binary Search
date: 2024-09-22

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


## Binary Search


### Find First and Last Position of Element in Sorted Array
[题目](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/description/)：
在有序数组中查找指定元素首次出现和末次出现的索引。不存在时给-1.<br>
Example 1:<br>
Input: nums = [5,7,7,8,8,10], target = 8<br>
Output: [3,4]<br>
```java
//先通过二分查找找到指定元素，再分别往前和往后找。
    public int[] searchRange(int[] nums, int target) {
        
        int left = 0;
        int right = nums.length-1;
        int res_left = -1;
        int res_right = -1;
        int cur = -1;
        while(left<=right){
            cur = (left + right)/2;
            if (nums[cur]==target){
                res_left = cur;
                res_right = cur;
                break;
            }
            if (nums[cur]>target){
                right = cur-1;
            }
            if (nums[cur]<target) {
                left = cur + 1;
            }
        }
        while (res_left>0 && nums[res_left-1] == target){
            res_left--;
        }
        while (res_right<nums.length-1 && nums[res_right+1] == target){
            res_right++;
        }
        return new int[]{res_left,res_right};
    }

```
