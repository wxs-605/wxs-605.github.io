# LeetCode 技巧


前两天家里搞卫生啥的，没有做题，今天终于搞得差不多了，赶紧刷几道题。总结一下今天刷到的技巧题。

## 只出现一次的数字
给你一个 非空 整数数组 nums ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

你必须设计并实现线性时间复杂度的算法来解决此问题，且该算法只使用常量额外空间。

### 思路
数组中只有一个数字出现一次，其余的均出现两次，可以考虑使用或运算，或运算的规则是同一异零，一个数与自己进行或运算，其结果是零，并且或运算满足交换律。

#### 实现过程
```C++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;
        for (auto num : nums)
            ans ^= num;
        return ans;
    }
};
```

- 时间复杂度：O(N)，遍历一次数组；
- 空间复杂度：O(1);


## 颜色分类
给定一个包含红色、白色和蓝色、共 n 个元素的数组 nums ，**原地** 对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

必须在不使用库内置的 sort 函数的情况下解决这个问题。

### 思路
对选择排序加以改造，第一次选择 0 交换到前面，然后选择 1 在交换到 0 的后面。

### 实现过程
```C++
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int len = nums.size();
        if (len == 1)
            return;
        int start = 0;
        for (int k = 0; k <= 1; k++) {
            for (int i = start; i < len; i++) {
                bool flag = false;
                int index = i;
                if (nums[i] == k) {
                    start++;
                    continue;
                }
                for (int j = i + 1; j < len; j++) {
                    if (nums[j] == k) {
                        index = j;
                        flag = true;
                        break;
                    }
                }
                if (!flag) {
                    break;
                }
                nums[index] = nums[i];
                nums[i] = k;
                start++;
            }
        }
    }
};
```

- 时间复杂度：最坏情况下，nums 中元素以 1 2 0 的顺序排序，需要对 nums 中三分之二的元素进行选择排序，故为O(N<sup>2</sup>)；
- 空间复杂度：O(1)；


## 下一个排列
整数数组的一个 排列  就是将其所有成员以序列或线性顺序排列。

例如，arr = [1,2,3] ，以下这些都可以视作 arr 的排列：[1,2,3]、[1,3,2]、[3,1,2]、[2,3,1] 。
整数数组的 下一个排列 是指其整数的下一个字典序更大的排列。更正式地，如果数组的所有排列根据其字典顺序从小到大排列在一个容器中，那么数组的 下一个排列 就是在这个有序容器中排在它后面的那个排列。如果不存在下一个更大的排列，那么这个数组必须重排为字典序最小的排列（即，其元素按升序排列）。

- 例如，arr = [1,2,3] 的下一个排列是 [1,3,2] 。
- 类似地，arr = [2,3,1] 的下一个排列是 [3,1,2] 。
- 而 arr = [3,2,1] 的下一个排列是 [1,2,3] ，因为 [3,2,1] 不存在一个字典序更大的排列。
给你一个整数数组 nums ，找出 nums 的下一个排列。

必须 原地 修改，只允许使用额外常数空间。

### 思路
第一次从后往前遍历，找到第一个非降序的元素，记为 nums[i] , 第二次从后往前找到第一个大于 nums[i] 的数，记为 nums[j] ，然后交换 nums[i] 和 nums[j] ，再将 i 后面的元素反转。

#### 实现过程
```C++
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int len = nums.size();
        if (!len || len == 1)
            return;
        int i = len - 2;
        while (i >= 0 && nums[i] >= nums[i + 1])
            i--;
        if (i >= 0) {
            int j = len - 1;
            while (j >= 0 && nums[j] <= nums[i])
                j--;
            std::swap(nums[i], nums[j]);
        }
        std::reverse(nums.begin() + i + 1, nums.end());
    }
};
```
- 时间复杂度：最坏情况下，遍历两次 nums ，故时间复杂度O(N);
- 空间复杂度：O(1)；


## 寻找重复数
给定一个包含 n + 1 个整数的数组 nums ，其数字都在 [1, n] 范围内（包括 1 和 n），可知至少存在一个重复的整数。

假设 nums 只有 一个重复的整数 ，返回 这个重复的数 。

你设计的解决方案必须 不修改 数组 nums 且只用常量级 O(1) 的额外空间。

### 思路
nums 中只有一个重复的整数，且只能使用常量空间， num 的大小还满足 [1, n] ，所以可以使用原地哈希的方法。计算出 nums 中的元素 num 的绝对值，若

- nums[num - 1] < 0 ：说明这个数已经出现过，是重复的数，也就是寻找的答案，直接退出遍历即可；

- nums[num - 1] > 0：说明这个数还未出现，将其置为负数，既：nums[num - 1] = -nums[num - 1] ;

最后复原 nums ，返回答案即可。

#### 实现过程
```C++
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int len = nums.size();
        int ans;
        for (int i = 0; i < len; i++) {
            int index = std::abs(nums[i]) - 1;
            if (nums[index] < 0) {
                ans = index + 1;
                break;
            } else
                nums[index] = -nums[index];
        }
        for (auto& num : nums) {
            if (num < 0)
                num = std::abs(num);
        }
        return ans;
    }
};
```

- 时间复杂度：最坏情况下，重复出现的数在 nums 的最后，找到 answer 跟复原 nums 共需遍历两次数组，故为O(N)；
- 空间复杂度：O(1);
