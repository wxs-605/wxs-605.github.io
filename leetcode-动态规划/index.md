# LeetCode 动态规划


今天刷了动态规划的题，总结一下其中的收获。

**一维动态规划思想**

使用动态规划解题，得出的解是全局最优解，那么可以知道，在求解的过程中每一个解都是最优的。既 dp[i] 是 i 位置处的最优解，dp[i - 1] 是 i - 1 状态的最优解。故动态规划的核心是找到这两者之间的关系。当然，这并不是绝对的，也有些题是找 dp[i] 与 dp[0...i - 1]之间的关系。
- dp[i] 与 dp[i - 1] 之间的关系；
- dp[i] 与 [ dp[0], dp[i - 1] ]之间的关系；

**多维动态规划思想**

思想与一维动态规划类似，状态转移方程 dp 是多维的，变成找 dp[i][j] 与 dp[i - 1][j] / dp[i][j - 1] / dp[i - 1][j - 1] 之间的关系；

## 打家劫舍
你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

### 思路
不能连续偷两家，就是 dp[i - 1] 对 dp[i] 不起作用；那我们可想到 dp[i] 与 dp[i - 2] 、 dp[i - 3] 的关系，这样就保证了不会连续偷两家而触发报警；

dp[i] = max(dp[i - 2], dp[i - 3]) + nums[i] ；


#### 实现过程
```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int len = nums.size();
        if (len == 1)
            return nums[0];
        if (len == 2)
            return std::max(nums[0], nums[1]);
        vector<int> dp(len);
        std::fill(dp.begin(), dp.end(), 0);
        dp[0] = nums[0];
        dp[1] = nums[1];
        for (int i = 2; i < len; i++) {
            if (i == 2)
                dp[i] = dp[i - 2] + nums[i];
            else
                dp[i] = std::max(dp[i - 2], dp[i - 3]) + nums[i];
        }
        return std::max(dp[len - 1], dp[len - 2]);
    }
};
```

- 时间复杂度：遍历一次 nums ,故为O(N)；
- 空间复杂度：使用了 dp 数组存储中间信息，故为O(N)；


## 完全平方数
给你一个整数 n ，返回 和为 n 的完全平方数的最少数量 。

完全平方数 是一个整数，其值等于另一个整数的平方；换句话说，其值等于一个整数自乘的积。例如，1、4、9 和 16 都是完全平方数，而 3 和 11 不是。

### 思路
一步一步找，从 1 找到 n。使用 dp 存储中间信息，当计算 dp[i] 时，从 1 找到  i ;：

dp[i] = dp[i - j * j] + 1； 其中 j<sup>2</sup> ∈ [1, i)；

#### 实现过程
```c++
class Solution {
public:
    int numSquares(int n) {
        vector<int> dp(n + 2);
        std::fill(dp.begin(), dp.end(), 0);
        for (int i = 1; i <= n; i++) {
            dp[i] = i;
            for (int j = 1; j * j <= i; j++)
                dp[i] = std::min(dp[i], dp[i - j * j] + 1);
        }
        return dp[n];
    }
};
```

- 时间复杂度：遍历了一次数组，计算 dp[i] 的时间复杂度为O(N<sup>0.5</sup>)，故总的时间复杂度为O(N<sup>1.5</sup>)；
- 空间复杂度：使用了状态方程 dp ，故为O(N)；


## 零钱兑换
给你一个整数数组 coins ，表示不同面额的硬币；以及一个整数 amount ，表示总金额。

计算并返回可以凑成总金额所需的 最少的硬币个数 。如果没有任何一种硬币组合能组成总金额，返回 -1 。

你可以认为每种硬币的数量是无限的。

### 思路
思路与完全平方数差不多，不过这里的可用的序列已经给出。一步一步算，从 1 算到 amount 。这里有个小剪枝，将 coins 序列从小到大排序一下，若 coins[i] > i ，说明下一个coins也会大于，此时可以直接退出遍历；

#### 实现过程
```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        if (!amount)
            return 0;
        vector<int> dp(amount + 2);
        std::fill(dp.begin(), dp.end(), INT_MAX);
        sort(coins.begin(), coins.end());
        dp[0] = 0;
        for (int i = 1; i <= amount; i++) {
            for (int j = 0; j < coins.size(); j++) {
                if (coins[j] > i)
                    break;
                if (dp[i - coins[j]] == INT_MAX)
                    continue;
                dp[i] = std::min(dp[i - coins[j]] + 1, dp[i]);
            }
        }
        if (dp[amount] == INT_MAX)
            return -1;
        return dp[amount];
    }
};
```

- 时间复杂度：O(N<sup>1.5</sup>；
- 空间复杂度：使用了一维的状态方程，故为O(N)；


## 最长递增子序列
给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。

子序列 是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。

### 思路
这道题就是寻找 dp[i] 与 [ dp[0], dp[i - 1] ] 之间的关系。这里说明一下为什么不能只找 dp[i - 1]的关系，若 nums[i - 1] 同时大于 nums[i] 、nums[i - 2]，但是又存在 nums[i - 2] < nums[i] < nums[i + 1] 的情况，这时 dp[i] 得到的就不是最优解。

#### 实现过程
```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int len = nums.size();
        if (len == 1)
            return 1;
        vector<int> dp(len + 2, 1);
        int ans = INT_MIN;
        for (int i = 1; i < len; i++) {
            for (int j = 0; j < i; j++)
                if (nums[i] > nums[j])
                    dp[i] = std::max(dp[i], dp[j] + 1);
            ans = std::max(ans, dp[i]);
        }
        return ans;
    }
};
``` 

-  时间复杂度：每次计算 dp[i] 时都要从 dp[0] 遍历到 dp[i - 1] ，故为O(N);
- 空间复杂度：使用了状数组 dp ，故为O(N)； 

## 乘积最大子数组
给你一个整数数组 nums ，请你找出数组中乘积最大的非空连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

测试用例的答案是一个 32-位 整数。

子数组 是数组的连续子序列。

### 思路
不考虑特殊情况下，dp[i] = max(dp[i] * nums[i]，nums[i]) ；若 nums[i] 是负数呢？会出现什么情况？这时表示最大值的 dp[i] 变成了最小值，继续计算下去，将会得到一个错误答案。因此，可以记录连续子数组的最大值和最小值，当 nums[i] 是负数时，最大值变成最小值，最小值变成最大值，此时只需要交换最大值和最小值，按照上面的规则继续计算即可。

#### 实现过程
```c++
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int len = nums.size();
        if (len == 1)
            return nums[0];
        int cur_max = nums[0];
        int cur_min = nums[0];
        int ans = nums[0];
        for (int i = 1; i < nums.size(); i++) {
            if (nums[i] < 0)
                swap(cur_max, cur_min);
            cur_max = max(cur_max * nums[i], nums[i]);
            cur_min = min(cur_min * nums[i], nums[i]);
            ans = max(cur_max, ans);
        }
        return ans;
    }
};
```

- 时间复杂度：遍历一次 nums ，故为O(N)；
- 空间复杂度：使用了常熟个变量，故为O(1)；

## 分割等和子集
给你一个 只包含正整数 的 非空 数组 nums 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

### 思路
分割等和子集，换一种角度思考，就是看给出的 nums 中是否有元素可以的和等于 sum 的一半( sum 是 nums 各元素之和，且 nums 中的元素不能重复使用)。此时这道题又与完全平方和、零钱兑换的思路一致了。这里有个剪枝，如果 sum 为奇数，那么不可能分割成两个相等的子集，因为两个偶数相加必然等于偶数。

#### 实现过程
```c++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = 0;
        for (auto& num : nums)
            sum += num;
        if (sum % 2 == 1 || nums.size() == 1)
            return false;
        sum /= 2;
        sort(nums.begin(), nums.end());
        vector<int> dp(sum + 2, 0);
        for (int i = 0; i < nums.size(); i++)
            for (int j = sum; j >= 1; j--) {
                if (nums[i] > j)
                    continue;
                dp[j] = max(dp[j], dp[j - nums[i]] + nums[i]);
            }
        return dp[sum] == sum;
    }
};
```

- 时间复杂度：遍历整个 nums ，每次从 target 遍历到 1 ，故为o(N*target)；
- 空间复杂度：使用了一维状态转移方程，故为O(N)；


## 最长有效括号
给你一个只包含 '(' 和 ')' 的字符串，找出最长有效（格式正确且连续）括号子串的长度。

### 思路
使用一维状态方程，考虑下面两种情况：
1. s[i] = ')' && s[i - 1] = '(' 时      dp[i] = 2 + dp[i - 2] ;
2. s[i] = ')' && s[i -dp[i - 1] - 1] = '(' 时    dp[i] = dp[i - 1] + dp[i -dp[i - 1] - 2] ;

情况1是刚好与前面的括号匹配，情况2是里面包含了一串括号；

#### 实现过程
```c++
class Solution {
public:
    int longestValidParentheses(string s) {
        int len = s.size();
        if (!len || len == 1)
            return 0;
        vector<int> dp(len + 1, 0);
        if (s[1] == ')' && s[0] == '(')
            dp[1] = 2;
        int ans = dp[1];
        for (int i = 1; i < len; i++) {
            if (s[i] == ')') {
                if (s[i - 1] == '(') {
                    dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
                } else if (i - dp[i - 1] > 0 && s[i - dp[i - 1] - 1] == '(')
                    dp[i] = dp[i - 1] +
                            ((i - dp[i - 1]) >= 2 ? dp[i - dp[i - 1] - 2] : 0) + 2;
                ans = std::max(ans, dp[i]);
            }
        }
        return ans;
    }
};
```

- 时间复杂度：遍历了一次括号序列，故为O(N)；
- 空间复杂度：使用了一维状态方程 dp ，故为O(N)；


## 最小路径和
给定一个包含非负整数的 m x n 网格 grid ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

说明：每次只能向下或者向右移动一步。

### 思路
找关系，找 dp[i][ij] 与 dp[i - 1][j]、 dp[i][j - 1] 之间的关系。因为每次只能向下或者向右移动，所以 dp[i][j] 只能由 dp[i - 1][j] 和 p[i][j - 1] 的其中之一得来。若 
-  dp[i - 1][j] < p[i][j - 1] ; dp[i][j] = dp[i - 1][j] + grid[i][j] ;
- dp[i - 1][j] >= p[i][j - 1] ; dp[i][j] = dp[i][j - 1] + grid[i][j] ;

#### 实现
```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        vector<vector<int>> dp(m + 2, vector<int>(n + 2, 0));
        for (int i = 0; i < m; i++)
            for (int j = 0; j < n; j++) {
                int temp = 0;
                if (i > 0)
                    temp = dp[i - 1][j];
                if (j > 0) {
                    if (i == 0)
                        temp = dp[i][j - 1];
                    else
                        temp = min(dp[i][j - 1], temp);
                }
                dp[i][j] = grid[i][j] + temp;
            }
        return dp[m - 1][n - 1];
    }
};
```

- 时间复杂度：遍历完整个 grid ，故为O(MN)；
- 空间复杂度：采用了二维 dp 数组，故为O(MN)；


## 不同路径
一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？

### 思路
机器人每次只能向下或者向右移动一步，所以 dp[i][j] = dp[i][j - 1] + dp[i - 1][j]；

- 时间复杂度：遍历二维数组，故为O(MN)；
- 空间复杂度：采用了二维 dp ，故为O(MN)；


## 编辑距离
给你两个单词 word1 和 word2， 请返回将 word1 转换成 word2 所使用的最少操作数  。

你可以对一个单词进行如下三种操作：

- 插入一个字符
- 删除一个字符
- 替换一个字符

### 思路
假设由 A 和 B 两个单词，以上三种操作相当于在二维 dp 中找 dp[i][j ]的关系：
- 插入一个字符：若 world1[i - 1] == world2[j - 1] ，dp[i][j] = dp[i - 1][j - 1] ;
- 否则替换一个字符 dp[i][j] = dp[i - 1][j - 1] + 1;
- 删除一个字符：dp[i][j] = dp[i - 1][j]  + 1或 dp[i][j - 1] + 1；
实际上就是找 dp[i][j] 与 dp[i - 1][j - 1] 、dp[i - 1][j] 、dp[i][j - 1] 的关系，最后让 dp[i][j] 等于三者中的最小值;

#### 实现思路
```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int len1 = word1.size();
        int len2 = word2.size();
        vector<vector<int>> dp(len1 + 1, vector<int>(len2 + 1, 0));
        for (int i = 0; i <= len1; i++)
            dp[i][0] = i;
        for (int i = 0; i <= len2; i++)
            dp[0][i] = i;
        for (int i = 1; i <= len1; i++)
            for (int j = 1; j <= len2; j++) {
                int left = dp[i][j - 1] + 1;
                int down = dp[i - 1][j] + 1;
                int left_down = dp[i - 1][j - 1];
                if (word1[i - 1] != word2[j - 1])
                    left_down++;
                dp[i][j] = min(left_down, min(left, down));
            }
        return dp[len1][len2];
    }
};
```

- 时间复杂度：O(MN), M、N 分别为 world1 和 world2 的长度；
- 空间复杂度：o(mn)，采用了二维 dp 数组；
