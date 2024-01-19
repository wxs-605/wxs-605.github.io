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

注意，在ACM模式下，使用pair需要加头文件
```
 #include <utility>.
```


## 滑动窗口最大值
给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。返回 滑动窗口中的最大值 。

### 思路
这道题可以说是考察大根堆的运用，采用大根堆存储遍历到的元素，若堆顶元素的下标大于或等于窗口的左边界，那么这个值就是窗口内的最大值，否则弹出堆顶元素，继续判断。依此类推，遍历完整个数组，即可找打窗口中的所有最大值。

#### 实现代码
```
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        priority_queue<pair<int ,int>> q;
        int i = 0;
        int length  = nums.size();
        for(;i < length, k > 0; i++, k--){
            q.push(make_pair(nums[i], i));
        }
        vector<int>ans;
        ans.push_back(q.top().first);
        int left_margin = 1;
        for(;i < length;i++){
            q.push(make_pair(nums[i], i));
            while(q.top().second < left_margin)
                q.pop();
            ans.push_back(q.top().first);
            left_margin++;
        }
        return ans;
    }
};
```

- 时间复杂度：最坏情况下，数组 nums 中的元素单调递增，最终大根堆中没有元素被移除。由于将一个元素放入堆的时间复杂度为 O(logN)，因此总时间复杂度为 O(NlogN);
- 空间复杂度：采用了大根堆存储遍历到的元素，故为O(N)；


## 数据流的中位数
中位数是有序整数列表中的中间值。如果列表的大小是偶数，则没有中间值，中位数是两个中间值的平均值。
- 例如 arr = [2,3,4] 的中位数是 3 。
- 例如 arr = [2,3] 的中位数是 (2 + 3) / 2 = 2.5 。
实现 MedianFinder 类:
- MedianFinder() 初始化 MedianFinder 对象。
- void addNum(int num) 将数据流中的整数 num 添加到数据结构中。
- double findMedian() 返回到目前为止所有元素的中位数。与实际答案相差 10-5 以内的答案将被接受。

### 思路
使用两个优先队列，一个是大根堆，一个是小根堆。大根堆存储输入数据中较小的一半数据，小根堆存储输入数据中较大的一半数据。插入数据时，若两个堆的元素个数一致，先插入到小根堆中，否则，插入到大根堆。当两个堆中的元素个数相同时，中位数就是两个堆对顶元素之和的一半；不相同时，中位数就是小根堆中的堆顶元素。


#### 实现过程
```
class MedianFinder {
    priority_queue<int, vector<int>, greater<int>> min_heap;
    priority_queue<int> max_heap;

public:
    MedianFinder() {}

    void addNum(int num) {
        if (min_heap.size() == max_heap.size()) {
            max_heap.push(num);
            min_heap.push(max_heap.top());
            max_heap.pop();
        } else {
            min_heap.push(num);
            max_heap.push(min_heap.top());
            min_heap.pop();
        }
    }

    double findMedian() {
        if (min_heap.size() != max_heap.size())
            return min_heap.top();
        else
            return (max_heap.top() + min_heap.top()) / 2.0;
    }
};

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder* obj = new MedianFinder();
 * obj->addNum(num);
 * double param_2 = obj->findMedian();
 */
```

**注意**
使用优先队列大小根堆时，要先引入头文件 queue ,同时，使用priority_queue默认是大根堆，使用小根堆时，需要这样写

```
#include<queue>	// 优先队列要引入这个头文件
priority_queue<int, vector, greater> name		// 小根堆的写法
```

- 时间复杂度：查找中位数的时间为O(1)，直接访问对顶元素即可；添加/删除 num 的时间为O(logN)，故为O(logN)；
- 空间复杂度：存储了所有 num ，故为O(N)；
