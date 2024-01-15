# LeetCode 堆


小结一下今天刷到的关于堆的题目。

## 数组中的第K个最大元素
给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。你必须设计并实现时间复杂度为 O(n) 的算法解决此问题。

### 思路
这道题中说明了，不用考虑重复元素，若需要考虑重复元素时，使用哈希表去重即可。维护一个容量为 K 的小根堆(优先队列 priority_queue )，先将前 K 个元素放入堆中，然后遍历整个数组，将每个元素与堆顶元素比较，若其大于堆顶元素，则弹出堆顶元素，并将当前的元素入堆；遍历完之后，对顶元素既是第 K 大的元素。

#### 实现代码
```
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        priority_queue<int, std::vector<int>, std::greater<int>> q;
        int length = nums.size();
        int i = 0;
        for(;i < k;i++){
            q.push(nums[i]);
        }
        for(;i < length;i++){
            if(nums[i] < q.top())
                continue;
            q.push(nums[i]);
            q.pop();
        }
        return q.top();
    }
};
```

- 时间复杂度：维护一个大小为 K 的堆，最坏情况下，数组中的元素为升序排列，故为O(NlogN)；
- 空间复杂度：使用大小为 K 的堆，故为O(K);


## 前 K 个高频元素
给你一个整数数组 nums 和一个整数 k ，请你返回其中出现频率前 k 高的元素。你可以按 任意顺序 返回答案。

### 思路
先用哈希表记录各个元素出现的次数，然后将这些记录对加入到一个vector中，以出现次数进行排序，倒数 K 个记录对即为前 K 个高频单词。

#### 实现代码
```
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> times;
        for (auto e : nums)
            times[e]++;
        vector<pair<int, int>>temp;
        for(auto & it : times){
            pair<int, int> t(it.second, it.first);
            temp.push_back(t);
        }
        sort(temp.begin(), temp.end());
        vector<int> ans;
        for(int i = temp.size() - 1;k > 0;i--,k--){
            ans.push_back(temp[i].second);
        }
        return ans;
    }
};

```

注意，在ACM模式下，使用pair需要加头文件 #include <utility>.
