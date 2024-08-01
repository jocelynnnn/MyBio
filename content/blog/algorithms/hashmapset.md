---
title: 算法（五） —— HashMap & HashSet
summary: HashMap & HashSet
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


## HashMap & HashSet


### 1. Two Sum
[题目](https://leetcode.com/problems/two-sum/description/)：从一个数组中找到和为给定数的两个数的序号。<br>
Example 1:<br>
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
<br>
```java
public int[] twoSum(int[] nums, int target) {
        int[] result = new int[2];
        HashMap<Integer,Integer> map = new HashMap<>();
        for (int i=0; i<nums.length; i++){
            map.put(target-nums[i],i);
        }
        for (int i=0; i<nums.length; i++){
            if (map.containsKey(nums[i]) && i!=map.get(nums[i])){
                result[0] = i;
                result[1] = map.get(nums[i]);
                break;
            }
        }
        return result;
    }
```

### 146. LRUCache
[题目](https://leetcode.com/problems/lru-cache/description/)：设计一个LRU缓存队列。
<br>**分析：** 用双向链表作为数据结构。基础地，需要头插和删除指定节点，两个方法。
<br>代码如下：

```java
class Node{
        int key;
        int val;
        Node prev;
        Node next;
        Node(int key, int val){
            this.key = key;
            this.val = val;
        }
    }
    Node head = new Node(-1,-1);
    Node tail = new Node(-1,-1);
    int cap;
    HashMap<Integer,Node> m = new HashMap<>();
    public LRUCache(int capacity) {
        cap = capacity;
        head.next = tail;
        tail.prev = head;
    }

    //头插
    private void addNode(Node newNode){
        Node temp = head.next;

        newNode.next = temp;
        newNode.prev = head;

        head.next = newNode;
        temp.prev = newNode;
    }

    private void deleteNode(Node delNode){
        Node pre = delNode.prev;
        Node next = delNode.next;

        pre.next = next;
        next.prev = pre;
    }
    public int get(int key) {
        if (m.containsKey(key)){
            Node resNode = m.get(key);
            int resVal = resNode.val;

            m.remove(key);
            deleteNode(resNode);
            addNode(resNode);
            m.put(key, head.next);
            return resVal;
        }
        return -1;
    }

    public void put(int key, int value) {
        if (m.containsKey(key)){
            Node curr = m.get(key);
            m.remove(key);
            deleteNode(curr);
        }
        if (m.size() == cap){
            m.remove(tail.prev.key);
            deleteNode(tail.prev);
        }
        addNode(new Node(key,value));
        m.put(key,head.next);
    }
```
### 128. Longest Consecutive Sequence
[题目](https://leetcode.com/problems/longest-consecutive-sequence/description/)：给定一个无序的数组，计算其中连续整数的最长长度，要求时间复杂度为O(1)。<br><br>
Example 1:
<br>
Input: nums = [100,4,200,1,3,2]<br>
Output: 4<br>
Explanation: The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.
<br><br>
**分析：** 首先使用HashSet存所有整数，把每一个数字视为连续数组的起始位，往后遍历，找到最长长度。但是这样做，会导致重复遍历，例如[0,1,2,3,4,5]，遍历后得到各自的最长长度为[6,5,4,3,2,1]。时间复杂度不符合要求。<br>
此时只需要进行剪枝，只要当前数字不是连续数字串的第一位，就不需要往后遍历，那么思路就很简单了，对于数字i，只要i-1也在序列中，则不需要往后遍历。
<br>代码如下：

```java
public int longestConsecutive(int[] nums) {
        if (nums.length == 0 || nums.length == 1){
            return nums.length;
        }
        int res=1;
        HashSet<Integer> s = new HashSet<>();
        for (int num:nums){
            s.add(num);
        }
        for (int num:s){
            int cur = 1;
            if (s.contains(num-1)){
                continue;
            }
            int next = num+1;
            while (s.contains(next)){
                cur ++;
                next++;
            }
            if(cur > res){
                res = cur;
            }
        }
        return res;
    }
```

### 73. Set Matrix Zeroes
[题目](https://leetcode.com/problems/set-matrix-zeroes/description/)：给定一个二维数组，若一个元素的初始值为0，则将其横排和竖列的所有值变更为0，只可在原数组上变更。
<br>
**分析：** 新开辟两个一维数组，记录横排和竖列应为0的下标。
<br>代码如下：

```java
public void setZeroes(int[][] matrix) {
        int n = matrix.length;
        int m = matrix[0].length;
        int[] a = new int[n];
        int[] b = new int[m];
        for (int i=0; i<n; i++){
            for (int j=0; j<m; j++){
                if (a[i]!=1 || b[j]!=1){
                    if (matrix[i][j] == 0){
                        a[i] = 1;
                        b[j] = 1;
                    }
                }
            }
        }
        for (int i=0; i<n; i++){
            for (int j=0; j<m; j++){
                if (a[i] == 1 || b[j] == 1){
                    matrix[i][j] = 0;
                }
            }
        }
    }
```


### LeetCode 380. Insert Delete GetRandom O(1)
[题目](https://leetcode.com/problems/insert-delete-getrandom-o1/description/)：实现一个数据结构，可以在时间复杂度O(1)內完成：1）插入一个数字；2）删除一个数字；3）从数组中随机取出一个数。
<br>
**思路：首先肯定会想到用一个list存所有数，然后通过随机生成下标，获取随机数。但是这样做的问题在于，无法在O(1)时间内完成元素的删除，因此可以使用一个HashMap记录下标。 在执行删除时，将list最后一个数替换到指定位置，然后将最后一位数删除即可。**
<br>

```java
class RandomizedSet {
    private ArrayList<Integer> list;
    private HashMap<Integer,Integer> map;
    public RandomizedSet() {
        list = new ArrayList<>();
        map = new HashMap<>();
    }

    public boolean insert(int val) {
        if (map.containsKey(val)){
            return false;
        }
        list.add(val);
        map.put(val,list.size()-1);
        return true;
    }

    public boolean remove(int val) {
        if (!map.containsKey(val)){
            return false;
        }
        int index = map.get(val);
        list.set(index,list.get(list.size()-1));
        map.put(list.get(index),index);
        list.remove(list.size()-1);
        map.remove(val);
        return true;
    }

    public int getRandom() {
        Random random = new Random();
        int index = random.nextInt(list.size());
        return list.get(index);
    }
}
```

### Leetcode 49. Group Anagrams
[题目](https://leetcode.com/problems/group-anagrams/description/)：给定一个字符串数组strs，将构成相同的字符串组合在一起。<br>
Example 1:<br>
Input: strs = ["eat","tea","tan","ate","nat","bat"]<br>
Output: [["bat"],["nat","tan"],["ate","eat","tea"]]
<br>
**思路：这道题要对字符串数组进行分类，也就是说我们需要知道当前字符串应该被放到第几类中。那么我们可以用一个map，记录每一个分类包含的字母，及其在第几类中（index）。 接下来只要对每一个字符串，判断它属于哪个类别。<br>判断方法，这里使用的是对字符串中的每个字符进行字典序排序，然后直接比较排序后的字符串是否相等即可。**
<br>代码如下：
```java
public List<List<String>> groupAnagrams(String[] strs) {
        HashMap<String,Integer> map = new HashMap<>();
        List<List<String>> res = new ArrayList<>();

        for (String s: strs){
            char[] cs = s.toCharArray();
            Arrays.sort(cs);
            String newS = new String(cs);
            if(!map.containsKey(newS)){
                List<String> cur = new ArrayList<>();
                cur.add(s);
                res.add(cur);
                map.put(newS,res.size()-1);
            }else{
                res.get(map.get(newS)).add(s);
            }
        }
        return res;
    }
```

### 350. Intersection of Two Arrays II
[题目](https://leetcode.com/problems/intersection-of-two-arrays-ii/description/)：
给定两个整型数组，找到两个数组的重复部分。<br>
**分析：数组内的数是可以重复的，因此可以用一个HashMap来记录数字出现的次数。**<br>
```java
public int[] intersect(int[] nums1, int[] nums2) {
        Map<Integer,Integer> map = new HashMap<>();
        List<Integer> result = new ArrayList<>();

        for (int i=0; i<nums1.length; i++){
            if (map.containsKey(nums1[i])){
                map.put(nums1[i],map.get(nums1[i])+1);
            }else{
                map.put(nums1[i],1);
            }
        }
        for (int i=0; i<nums2.length; i++){
            if(map.containsKey(nums2[i])){
                result.add(nums2[i]);
                if(map.get(nums2[i])==1){
                    map.remove(nums2[i]);
                }else{
                    map.put(nums2[i],map.get(nums2[i])-1);
                }
            }
        }
        int[] a = new int[result.size()];
        for (int i=0; i<result.size();i++){
            a[i] = result.get(i);
        }
        return a;
    }
```