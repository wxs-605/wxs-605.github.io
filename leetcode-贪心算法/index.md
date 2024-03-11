# LeetCode 贪心算法


今天刷了贪心算法，小结一下这些题目的思路。

## 买卖股票的最佳时机
给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。

你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

### 思路
用两个变量分别记录最大利润和股票的最低价，先计算先前买的股票的最大利润，既 max_profit = max(max_profit, prices[i] - purchase) , 再判断今天是否是股票的最低价，既 purchase = min(purchase, prices[i])；注意，这两个步骤不能反，因为你不能出售未来买的股票。

#### 代码实现
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int length = prices.size();
        if (length == 1)
            return 0;
        int max_profit = 0;
        int purchase = prices[0];
        for (int i = 0; i < length; i++) {
            max_profit = max(max_profit, prices[i] - purchase);
            purchase = min(purchase, prices[i]);
        }
        return max_profit;
    }
};
```

- 时间复杂度：遍历了一次 prices ，故为O(N)；
- 空间复杂度：使用了三个临时变量，故为O(1)；


## 买卖股票的最佳时机II
给你一个整数数组 prices ，其中 prices[i] 表示某支股票第 i 天的价格。

在每一天，你可以决定是否购买和/或出售股票。你在任何时候 最多 只能持有 一股 股票。你也可以先购买，然后在 同一天 出售。

返回 你能获得的 最大 利润 。

### 思路
这道题典型的贪心算法，我先购买第一个的股票，记录下购买时间 purchase_time = 0 和购买价格 purchase_price = prices[i]，紧接着开始遍历股票序列：
1. 如果当前的股票价格高于我购买时的价格，则出售手里的股票，在重新买回；
- profit += prices[i] - purchase_prices; // 计算总的利润之和
- purchase_time = i; // 记录购买时间
- purchase_prices = prices[i]; // 记录购买价格

2. 如果当前股票价格低于我之前所购买的股票价格：
则重新购买这只股票，之前购买的股票所得利润为0（相当于当天买当天卖）；
- purchase_time = i; // 记录购买时间
- purchase_prices = prices[i]; // 记录购买价格

#### 代码实现
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int profit = 0;
        int purchase_prices = prices[0];
        int purchase_time = 0;
        for (int i = 0; i < prices.size(); i++) {
            if (prices[i] > purchase_prices) {
                profit += prices[i] - purchase_prices;
                purchase_time = i;
                purchase_prices = prices[i];
            } else {
                purchase_time = i;
                purchase_prices = prices[i];
            }
        }
        return profit;
    }
};
```

- 时间复杂度：遍历了一次股票序列，故为O(N);
- 空间复杂度，使用几个临时变量，故空间复杂度为(1)；


## 跳跃游戏
给你一个非负整数数组 nums ，你最初位于数组的 第一个下标 。数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标，如果可以，返回 true ；否则，返回 false 。

### 思路
如果可以从开头跳到结尾，那么必然可以从结尾跳到开头。所以可以从结尾往前跳，一个一个判断，能跳到开头，就返回true，否则返回false。

#### 实现过程
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        if (nums.size() == 1)
            return true;
        int cur = nums.size() - 1;
        int pre = cur - 1;
        while (pre >= 0) {
            if (nums[pre] >= cur - pre) {
                cur = pre;
                pre -= 1;
            } else
                pre--;
        }
        if (cur == 0)
            return true;
        return false;
    }
};
```

- 时间复杂度：遍历了一次 nums ，故为O(N)；
- 空间复杂度：O(1);


## 跳跃游戏 II
给定一个长度为 n 的 0 索引整数数组 nums。初始位置为 nums[0]。

每个元素 nums[i] 表示从索引 i 向前跳转的最大长度。换句话说，如果你在 nums[i] 处，你可以跳转到任意 nums[i + j] 处:

- 0 <= j <= nums[i] 
- i + j < n
返回到达 nums[n - 1] 的最小跳跃次数。生成的测试用例可以到达 nums[n - 1]。

### 思路
一跳接着一跳跳。先找出从当前可以跳到的下一个区间，跳数加一，再以找到下一个区间作为当前这一跳的区间，再去找下一跳的区间，以此类推，直到跳到最后一个下标。

#### 实现过程
```cpp
class Solution {
public:
    int jump(vector<int>& nums) {
        int start = 0;
        int end = 1;
        int len = nums.size();
        int result = 0;
        while (end < len) {
            int maxDistance = INT_MIN;
            for (int i = start; i < end; i++) {
                maxDistance = max(maxDistance, i + nums[i]);
            }
            start = end;
            end = maxDistance + 1;
            result += 1;
        }
        return result;
    }
};
```

- 时间复杂度：在 while 循环里，实际上遍历了一次 nums ，故为O(N)；
- 空间复杂度：O(1);


## 划分字母区间
给你一个字符串 s 。我们要把这个字符串划分为尽可能多的片段，同一字母最多出现在一个片段中。

注意，划分结果需要满足：将所有划分结果按顺序连接，得到的字符串仍然是 s 。

返回一个表示每个字符串片段的长度的列表。

### 思路
记录每个字符最后出现的位置，从字符开始出现 start 到最后出现 end 这个区间中，如果存在字符的最后出现位置超过 end ，那么就更新 end ，当遍历到 end 时，说明这个区间的字符已经遍历完了，将这个区间加入到 answer 中即可，然后更新 start 和 end ，依此类推直到遍历完整个字符串。

#### 代码实现
```cpp
class Solution {
public:
    vector<int> partitionLabels(string s) {
        unordered_map<char, int> map;
        int len = s.size();
        vector<int> ans;
	  // 记录字符的最后出现位置
        for (int i = 0; i < len; i++) {
            map[s[i]] = i;
        }
        int start = 0;
        int end = map[s[0]];
        for (int i = 0; i < len; i++) {
            if (i == end) {
                ans.push_back(end - start + 1);
                start = i + 1;
                if (i != len - 1)
                    end = map[s[i + 1]];
                continue;
            }
            if (i < end && map[s[i]] > end)
                end = map[s[i]];
        }
        return ans;
    }
};
```

- 时间复杂度：遍历了两次字符串，故为O(N)；
- 空间复杂度：采用了哈希表存储每个字符出现的最后位置，最多有26个字符，故为O(1)；
