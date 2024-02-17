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
