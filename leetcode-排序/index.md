# LeetCode 排序


# 多级排序
此类题目一般涉及不同情况的排序，如两元素值不等时升序排序，值相等时降序排序。通常调用库函数`sort`实现，在里面使用`lambda`表达式来自定义排序规则，如刚刚说的情况：
```cpp
std::sort(nums.begin(), nums.end(), [](vector<int>& i, vector<int>& j){
	if(i[0] != j[0])
		return i[0] < j[0];
	return i[1] > j[1];
});
```

多级排序完成后再按某种规则处理即可得到答案。

## 根据身高重建队列
假设有打乱顺序的一群人站成一个队列，数组 people 表示队列中一些人的属性（不一定按顺序）。每个 people[i] = [hi, ki] 表示第 i 个人的身高为 hi ，前面 正好 有 ki 个身高大于或等于 hi 的人。

请你重新构造并返回输入数组 people 所表示的队列。返回的队列应该格式化为数组 queue ，其中 queue[j] = [hj, kj] 是队列中第 j 个人的属性（queue[0] 是排在队列前面的人）。

### 思路
理解好 people[i] 的含义之后就好做了。先将 people 按照身高不同时降序、身高相同时升序排列，得到一个从高到矮的队列。因为按照身高进行降序排序，对于每个元素，在其之前的元素的个数，就是大于等于他的元素的数量，而身高相同时按照人数升序排序，希望 k 大的尽量在后面，减少插入操作的次数，同时也为了保证正确性，因为`[5,3]`必然在`[5,2]`的后面。然后逐个把这些人插入到**答案队列 ans**中即可：

1. 如果 ans 的长度小于 people[i][1]，则将这个人插入到队列的最后面；

2. 如果 ans  的长度大于 people[i][1]，则将这个人插入到第 people[i][1] 个位置上，刚好满足条件；

#### 实现过程
```cpp
class Solution {
public:
    vector<vector<int>> reconstructQueue(vector<vector<int>>& people) {
        std::sort(people.begin(), people.end(),
                  [](vector<int>& i, vector<int>& j) {
                      if (i[0] != j[0])
                          return i[0] > j[0];
                      return i[1] < j[1];
                  });
        vector<vector<int>> ans;
        for (auto& element : people) {
            if (ans.size() <= element[1])
                ans.push_back(element);
            else
                ans.insert(ans.begin() + element[1], element);
        }
        return ans;
    }
};
```

- 时间复杂度：我们使用库函数进行排序，需要的时间为O(NlogN)，再遍历一次排序后的数组，花费为O(N)，故总的为O(NlogN)；
- 空间复杂度：排序时栈空间的花费，为O(logN)；



## 归并排序
归并排序的思路是先对数组进行递归对半划分，直到划分成单个元素，然后对这些划分好的元素进行两两进行归并，最终得到整体有序。在归并时需要用到辅助数组，先把归并好的元素暂存到辅助数组中，最后再把暂存数组中的元素复制回原数组。


## 交易逆序对的总数
在股票交易中，如果前一天的股价高于后一天的股价，则可以认为存在一个「交易逆序对」。请设计一个程序，输入一段时间内的股票交易记录 record，返回其中存在的「交易逆序对」总数。

### 首先想到的思路是使用选择排序的模板来计算，显然，时间超限了。我们换一种思路，考虑到逆序对是前者大于后者，且均是两两比较的，所以可以想到用归并排序加以改造来进行解题。首先，我们递归地对数组两两划分，直到每个部分只有一个元素；然后对划分好的两两部分进行归并，设归并的前半部分元素下标 i 从 start 到 mid，后半元素下标 j 从 mid + 1 到 end。在归并的比较过程中：
1. 如果  nums[i] <= num [j]，此时后者大于前者，不是逆序对；
2. 如果 nums[i] > nums[j]，说明当前是逆序对，但是由于 前半部分元素是递增的，nums[i] > nums[j] ，那么必然 nums[i + 1]...nums[mid] 都会大于 nums[j]，这些都是逆序对，故逆序对数量为 ans += mid - i + 1;

归并排序完即可计算出所有逆序对的数量。

#### 实现过程
```cpp
class Solution {
    int ans;
    void divide(vector<int>& record, int start, int end, vector<int>& temp) {
        if (start < end) {
            int mid = (start + end) / 2;
            divide(record, start, mid, temp);
            divide(record, mid + 1, end, temp);
            merge(record, start, mid, end, temp);
        }
    }
    void merge(vector<int>& record, int start, int mid, int end,
               vector<int>& temp) {
        int i = start;
        int j = mid + 1;
        int k = 0;
        while (i <= mid && j <= end) {
            if (record[i] <= record[j]) {
                temp[k++] = record[i++];
            } else {
                ans += mid - i + 1;
                temp[k++] = record[j++];
            }
        }
        while (i <= mid)
            temp[k++] = record[i++];
        while (j <= end)
            temp[k++] = record[j++];
        for (i = 0; i < k; i++)
            record[i + start] = temp[i];
    }

public:
    int reversePairs(vector<int>& record) {
        int len = record.size();
        if (!len)
            return 0;
        ans = 0;
        vector<int> temp(len + 1, 0);
        divide(record, 0, len - 1, temp);
        return ans;
    }
};
```

- 时间复杂度：采用了归并排序，故时间复杂度为o(NlogN)；
- 空间复杂度：采用了辅助数组，故为O(N)；
