# LeetCode 回溯


 今天刷的专题是回溯，对于回溯这块，比较陌生，这里记录一下一些题的思路以及大神总结的回溯题思路。

#### 回溯算法
   回溯算法是一种在搜索空间中寻找解的算法。它采用试错的思想，尝试分步地去解决一个问题。在求解过程中，当发现当前的分步答案不能得到正确的解时，就取消上一步或几步的计算，并进行新的尝试。

回溯算法通常应用于组合问题、排列问题、选择问题等。它的基本思路是：从第一个可能的动作开始搜索，每当搜索到一个状态时，先判断这个状态是否满足问题的约束条件，如果满足约束条件，则进入下一个状态继续搜索；如果不满足约束条件，则返回上一个状态，进行其他可能的动作。这个过程就像在一个树形结构中遍历所有的节点，因此回溯算法也被称为“试探法”。

**怎样写回溯算法？**

- 画出递归树，找到状态变量(回溯函数的参数)
- 根据题意，确立结束条件
- 找准选择列表(与函数参数相关)，与第一步紧密关联
- 判断是否需要剪枝
- 做出选择，递归调用，进入下一层
- 撤销选择

遇到回溯算法相关的题目，可根据这个步骤思考，得到最终的实现代码。

*注意*

由于下面的题都是关于回溯算法的，故思路都是差不多的，只是条件不一样，重点是结合上面的过程和下面的实现代码强化对这类题的思考，对这类题形成一种思路而不是束手无措。

## 全排列
给定一个不含重复数字的数组 nums ，返回其 所有可能的全排列 。你可以 **按任意顺序** 返回答案。

### 思路
采用由深度优先搜索改造成的回溯函数，先一步步“试错”，找到最终答案，然后撤销选择，遍历完整个状态空间。

#### 实现过程
```
class Solution {
public:
    void backTrack(vector<vector<int>> & ans,vector<int>&nums, int start, int length){
	  // 递归结束条件，遍历到nums中的最后一个元素
        if(start == length){
            ans.push_back(nums);
            return;
        }
	
	  // 选择列表为未出现过的元素，这里由于是全排列问题，生成的状态都是有可能的，故不需要剪枝
        for(int i = start;i < length; i++){

		// 试错，然后进入下一层递归
            swap(nums[i], nums[start]);
            backTrack(ans, nums, start + 1, length);

		// 撤销选择
            swap(nums[i], nums[start]);
        }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> ans;
        backTrack(ans, nums, 0, nums.size());
        return ans;
    }
};
```

- 时间复杂度：O(N*N!)；
- 空间复杂度：全排列问题需要遍历到最后一个元素，故为O(N)；


## 子集
给你一个整数数组 nums ，数组中的元素互不相同。返回该数组所有可能的子集（幂集）。解集不能包含重复的子集。你可以按任意顺序返回解集。

### 思路
采用回溯算法，递归树中使用一个参数 start，来标识当前的选择列表的起始位置。标识每一层的状态，然后start  + 1，进入下一层的递归，直到找到所有子集。

#### 实现代码
```
class Solution {
public:
void backTrack(vector<vector<int>>&ans, vector<int>&nums, vector<int>&path, int start){
    // 此题特殊，找到的所有状态都是答案，故均需要加入到ans中；
    ans.push_back(path);
    for(int i = start; i < nums.size();i++){

	  // 当前路径中加入 nums[i]，生成下一个子集
        path.push_back(nums[i]);
        backTrack(ans, nums, path, i + 1);

	  // 撤销选择，删除加入的nums[i];
        path.pop_back();
    }
}
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int>path;
        backTrack(ans,nums, path, 0);
        return ans;
    }
};
```

- 时间复杂度：一共是2<sup>N</sup>个状态，每个状态的构建需要O(N)，故总的为O(N*2<sup>N</sup>)；
- 空间复杂度：使用了一个数组存储路径，故为O(N)；


## 组合总和
给你一个 无重复元素 的整数数组 candidates 和一个目标整数 target ，找出 candidates 中可以使数字和为目标数 target 的 所有 不同组合 ，并以列表形式返回。你可以按 任意顺序 返回这些组合。

- candidates 中的 同一个 数字可以 无限制重复被选取 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

- 对于给定的输入，保证和为 target 的不同组合数少于 150 个。

### 思路
套用上面总结的回溯模板，找到、找准结束条件，选择列表即可。

#### 实现过程
```
class Solution {
public:
    void backTrack(vector<vector<int>>& ans, vector<int>&nums, vector<int> &path, int start, int sum, int target){
	  // 结束条件 => 当前元素和 sum == target
        if(sum == target){
            ans.push_back(path);
            return;
        }

        for(int i = start;i < nums.size();i++){

		// 剪枝操作，若当前 sum + nums[i] > target ，则说明其下一层的子状态都不是解，故不需要再进入下一层递归
            if(sum + nums[i] > target)
                continue;

		// 路径中加入当前元素值nums[i]，进入下一层递归
            path.push_back(nums[i]);
            backTrack(ans, nums, path, i, sum + nums[i], target);

		// 撤销选择
            path.pop_back();
        }
    }
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int>path;
        backTrack(ans, candidates,path, 0, 0, target);
        return ans;
    }
};
```

- 时间复杂度：O(N)，N为所有可行解的长度之和。搜索递归树的时间复杂度取决于搜索树所有叶子节点的深度之和，即所有可行解的长度之和；
- 空间复杂度：最坏情况下，target 由 target 个 1 组成，需要递归 O(target)层，故为O(target)；


## 括号生成
数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

#### 实现过程
```
class Solution {
public:
    void backTrack(vector<string>&ans, string cur, int left, int right, int len){
	  // n 对括号序列的长度为 2 * n，若当前字符串的长度为 2 * len，则其为问题的解之一
        if(len * 2== cur.size()){
            ans.push_back(cur);
            return;
        }

	  // 左括号数量没有达到 len 时，可以继续生成左括号，然后递归
        if(left < len){
            cur.push_back('(');
            backTrack(ans,cur, left + 1, right, len);
            cur.pop_back();
        }

	  // 若左括号数量为 n 之后，右括号数量不够，则生成右括号
        if(right < left){
            cur.push_back(')');
            backTrack(ans,cur, left, right + 1, len);
            cur.pop_back();
        }
    }
    vector<string> generateParenthesis(int n) {
        vector<string>ans;
        string cur = "";
        backTrack(ans,cur,0,0,n);
        return ans;
    }
};
```

- 时间复杂度：O(2<sup>2*N</sup> * N);
- 空间复杂度：需要的空间取决于递归栈的深度，每一层递归函数需要 O(1) 的空间，最多递归 2*N 层，因此空间复杂度为 O(N)；
