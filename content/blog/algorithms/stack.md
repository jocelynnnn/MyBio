---
title: 算法（四） —— Stack
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


## Stack

### 155. Min Stack
[题目](https://leetcode.com/problems/min-stack/description/)：设计一个堆，并实现以下方法，要求每个方法的时间复杂度都在O(1)。
<br>
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

### Leetcode 232. Implement Queue using Stacks
[题目](https://leetcode.com/problems/implement-queue-using-stacks/description/)：用两个栈实现一个队列。
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

### 150. Evaluate Reverse Polish Notation
[题目](https://leetcode.com/problems/evaluate-reverse-polish-notation/description/)：逆波兰表示法计算

Example 1:<br>
Input: tokens = ["2","1","+","3","*"]<br>
Output: 9<br>
Explanation: ((2 + 1) * 3) = 9

Example 2:<br>
Input: tokens = ["4","13","5","/","+"]<br>
Output: 6<br>
Explanation: (4 + (13 / 5)) = 6

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

### 224. Basic Calculator
[题目](https://leetcode.com/problems/basic-calculator/description/) ：实现一个基础的加减法计算器。
<br>
**解法：** 由于这道题只有加减法，这道题的计算只需要使用一个栈，记录当前数字和符号即可。而计算的方式可以简化成两步：<br>第一步：result \*= sign，<br>第二步：result += number。<br><br>**按'+', '-', '(', ')'和数字五种情况分析：**<br>**1. 数字：** 输入的数字并不一定是个位数，所以如果当前是数字，需要与前序数字整合，number起初为0：number = 10 * number + (int)(c - '0'); <br>
**2.+ ：** result += sign * number;
            number = 0;
            sign = 1;<br>
**3.- ：** result += sign * number;
            number = 0;
            sign = -1;<br>
**4.( :** 前序答案要入栈，等待括号里的计算完成后，再行计算。<br>
**5.) :** 当读到右括号时，括号内的计算已经完成了，并记录在当前的number和sign中，此时，只要把当前括号中的计算结果与栈内结果合并计算，那么此时需要从栈中弹出前一个符号及数字，并计算，记录在result中。
<br><br>代码如下：

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

### 20. Valid Parentheses
[题目](https://leetcode.com/problems/valid-parentheses/)：给定String，包含 '(', ')', '{', '}', '[' and ']'，检查括号是否匹配。
<br> **解法：Stack简单题。** 
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

### 1472. Design Browser History
[题目](https://leetcode.com/problems/design-browser-history/description/)：实现浏览器的前进后退功能。<br>
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

```

### 1209. Remove All Adjacent Duplicates in String II
[题目](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string-ii/description/)：给定一个String s和一个整型k，去掉s中连续出现k次的字符，并输出最终结果。<br>
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

### 1249. Minimum Remove to Make Valid Parentheses
[题目](https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/description/)：移除字符串中最少的字符，使得括号对有效。<br>
Example 1:<br>
Input: s = "lee(t(c)o)de)"<br>
Output: "lee(t(c)o)de"<br>
Explanation: "lee(t(co)de)" , "lee(t(c)ode)" would also be accepted.
<br>
**解法：使用栈，记录不匹配的括号对，额外一个栈记录index，即待删除的字符。最后使用StringBuilder的deleteCharAt去除不需要的字符。**

```java
public String minRemoveToMakeValid(String s) {
        Stack<Character> stack = new Stack<>();
        Stack<Integer> stackIndex = new Stack<>();
        StringBuilder sb = new StringBuilder(s);

        for (int i=0; i<s.length(); i++){
            if (s.charAt(i) == '('){
                stack.push('(');
                stackIndex.push(i);
            }
            if (s.charAt(i) == ')'){
                if (!stack.isEmpty() && stack.peek() == '('){
                    stack.pop();
                    stackIndex.pop();
                }else{
                    stack.push(')');
                    stackIndex.push(i);
                }
            }
        }
        while(!stackIndex.isEmpty()){
            sb.deleteCharAt(stackIndex.pop());
        }
        return sb.toString();
    }
```

### 735. Asteroid Collision
[题目](https://leetcode.com/problems/asteroid-collision/description/)：模拟小行星碰撞，给定一个数组，每个数字的正负表示方向，正为向右，负为向左，数字绝对值为行星质量，发生碰撞时，质量小的会爆炸消失，质量相同时一起爆炸消失。求最终行星系结果。<br>
Example 1:<br>
Input: asteroids = [5,10,-5]
Output: [5,10]<br>
Explanation: The 10 and -5 collide resulting in 10. The 5 and 10 never collide.
<br>
**分析：数据结构使用栈。只有栈顶元素向右，当前元素向左时可能相撞。撞击可能是连续发生的，因此当可能发生撞击时，需要一个循环，来判断撞击是否会终止。**

```java
public int[] asteroidCollision(int[] asteroids) {
        Stack<Integer> stack = new Stack<>();
        for (int item: asteroids){
            if (!stack.isEmpty() && item<0){
                if (stack.peek() < 0){
                    stack.push(item);
                }else if(stack.peek()>0){
                    while (!stack.isEmpty() && stack.peek()>0 && stack.peek() < Math.abs(item)) {
                        stack.pop();
                    }
                    if (stack.isEmpty() || stack.peek()< 0){
                        stack.push(item);
                    }
                    if (stack.peek() == Math.abs(item)){
                        stack.pop();
                    }
                }
            }else{
                stack.push(item);
            }
        }
        int num = stack.size();
        int[] result = new int[num];
    
        for (int i=num-1; i>=0;i--){
            result[i] = stack.pop();
        }
        return result;
    }
```
