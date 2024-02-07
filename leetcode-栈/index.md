# LeetCode 栈


总结一下今天刷栈类题目的思路或学到的思路。

## 有效的括号
给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。有效字符串需满足：
1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。
3. 每个右括号都有一个对应的相同类型的左括号。

### 思路
这是道简单题，直接用栈就可解决。遍历括号序列，如果是左括号，就加入到栈中，如果遇到右括号，则弹出栈顶的括号，如果不i配，可直接返回false；以此类推，遍历完这个序列。

- 时间复杂度：遍历一遍括号序列，故为O(N)；
- 空间复杂度：最坏情况下，序列的最后一个字符为右括号，其余全为左括号，栈中存储了N - 1个括号，故为O(N)；


## 最小栈
设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。实现MinStack类：
- MinStack() 初始化堆栈对象。
- void push(int val) 将元素val推入堆栈。
- void pop() 删除堆栈顶部的元素。
- int top() 获取堆栈顶部的元素。
- int getMin() 获取堆栈中的最小元素。

### 思路
就是实现一个栈嘛。采用单向链表的方式实现，其中栈的先进先出特性，可对链表采用头插法实现。链表带头节点，每次插入一个元素时，就将节点插入到头节点的后面。

#### 实现过程
```cpp
class Node {
public:
    int val;
    Node* next;
    Node(int value) : val(value) { next = nullptr; }
};
class MinStack {
    Node* head;

public:
    MinStack() { head = new Node(INT_MAX); }

    void push(int val) {
        head->val = min(val, head->val);
        Node* p = new Node(val);
        p->next = head->next;
        head->next = p;
    }

    void pop() {
        Node* temp = head->next;
        head->next = temp->next;
        if (head->val == temp->val) {
            head->val = INT_MAX;
            Node* p = head->next;
            while (p) {
                head->val = min(head->val, p->val);
                p = p->next;
            }
        }
        delete temp;
    }

    int top() {
        if (head->next)
            return head->next->val;
        else
            return -1;
    }

    int getMin() { return head->val; }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(val);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
```


## 每日温度
给定一个整数数组 temperatures ，表示每天的温度，返回一个数组 answer ，其中 answer[i] 是指对于第 i 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 0 来代替。

### 思路
采用单调栈解决，栈中的元素有序。使用一个栈来记录当前遇到的最高温度。当栈为空时，当前元素的坐标入栈；若当前元素小于栈顶元素，则将当前元素的坐标入栈；当栈不为空且栈顶元素小于当前元素时，更新栈顶元素对应的答案 ans[s.top()] = i - s.top() ；然后弹出栈顶元素，更新新栈顶元素对应的答案，直到栈为空，最后将当前元素的坐标入栈。

#### 实现过程
```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        stack<int> stk;
        int len = temperatures.size();
        vector<int> ans(len);
        fill(ans.begin(), ans.end(), 0);
        for (int i = 0; i < len; i++) {
            while (!stk.empty() && temperatures[i] > temperatures[stk.top()]) {
                ans[stk.top()] = i - stk.top();
                stk.pop();
            }
            stk.push(i);
        }
        return ans;
    }
};
```

- 时间复杂度：遍历一遍 nums 数组，故为O(N)；
- 空间复杂度：最坏情况下每日温度为递减序列，栈中存储了所有元素，故为O(N);
